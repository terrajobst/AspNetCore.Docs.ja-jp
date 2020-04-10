---
title: ASP.NETコアSignalRJavaScript クライアント
author: bradygaster
description: コアSignalRJavaScript クライアントASP.NET概要。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994584"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NETコアSignalRJavaScript クライアント

作成者: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NETコアSignalRJavaScript クライアント ライブラリを使用すると、開発者はサーバー側のハブ コードを呼び出します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="install-the-opno-locsignalr-client-package"></a>クライアントSignalRパッケージをインストールする

SignalR JavaScript クライアント ライブラリは[npm](https://www.npmjs.com/)パッケージとして提供されます。 次のセクションでは、クライアント ライブラリをインストールするさまざまな方法について説明します。

### <a name="install-with-npm"></a>npm でインストールする

Visual Studio を使用している場合は、ルート フォルダー内で**パッケージ マネージャー コンソール**から次のコマンドを実行します。 Visual Studio コードの場合は、**統合ターミナル**から次のコマンドを実行します。

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm はパッケージの内容を*node_modules\\*フォルダにインストールします。 *wwwroot\\lib*フォルダの下に*signalr*という名前の新しいフォルダを作成します。 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm はパッケージの内容を*node_modules\\*フォルダにインストールします。 *wwwroot\\lib*フォルダの下に*signalr*という名前の新しいフォルダを作成します。 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。

::: moniker-end

要素内SignalRの JavaScript`<script>`クライアントを参照します。 次に例を示します。

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>コンテンツ配信ネットワーク (CDN) を使用する

npm の前提条件なしでクライアント ライブラリを使用するには、クライアント ライブラリの CDN ホストコピーを参照します。 次に例を示します。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

クライアント ライブラリは、次の CDN で使用できます。

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [アンプク](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [アンプク](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>LibMan でインストールする

[LibMan](xref:client-side/libman/index)は、CDN ホスト型クライアント ライブラリから特定のクライアント ライブラリ ファイルをインストールするために使用できます。 たとえば、縮小された JavaScript ファイルのみをプロジェクトに追加します。 この方法の詳細については、「[クライアント ライブラリSignalRの追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。

## <a name="connect-to-a-hub"></a>ハブへの接続

次のコードは、接続を作成して開始します。 ハブの名前は大文字と小文字を区別しません。

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>クロスオリジン接続

通常、ブラウザーは要求されたページと同じドメインから接続を読み込みます。 ただし、別のドメインへの接続が必要な場合があります。

悪意のあるサイトが別のサイトから機密データを読み取らないようにするために、[既定では、クロスオリジン接続](xref:security/cors)は無効になっています。 クロスオリジンリクエストを許可するには、クラスでそれを有効にします`Startup`。

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>クライアントからハブ メソッドを呼び出す

JavaScript クライアントは、ハブ接続の[呼び出し](/javascript/api/%40aspnet/signalr/hubconnection#invoke)メソッドを介してハブ上のパブリック メソッドを呼び出[します](/javascript/api/%40aspnet/signalr/hubconnection)。 この`invoke`メソッドは、次の 2 つの引数を受け取ります。

* ハブ メソッドの名前。 次の例では、ハブのメソッド名は`SendMessage`です。
* ハブ メソッドで定義されている引数。 次の例では、引数名は`message`です。 このコード例では、Internet Explorer 以外のすべての主要ブラウザの現在のバージョンでサポートされている矢印関数構文を使用しています。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> AzureSignalRサービスを*サーバーレス モード*で使用している場合は、クライアントからハブ メソッドを呼び出すことはできません。 詳細については、[SignalRサービスのドキュメントを参照してください](/azure/azure-signalr/signalr-concept-serverless-development-config)。

この`invoke`メソッドは JavaScript[プロミス](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。 サーバー`Promise`上のメソッドが戻るときに、戻り値 (存在する場合) で解決されます。 サーバー上のメソッドがエラーをスローした場合、`Promise`はエラー メッセージで拒否されます。 メソッド`then`自体`catch`を使用して、`Promise`これらのケース (または`await`構文) を処理します。

この`send`メソッドは JavaScript`Promise`を返します。 メッセージ`Promise`がサーバーに送信されると、 が解決されます。 メッセージの送信中にエラーが発生した場合`Promise`、 はエラー メッセージで拒否されます。 メソッド`then`自体`catch`を使用して、`Promise`これらのケース (または`await`構文) を処理します。

> [!NOTE]
> を`send`使用しても、サーバーがメッセージを受信するまで待機しません。 したがって、サーバーからデータやエラーを返す可能性はありません。

## <a name="call-client-methods-from-hub"></a>ハブからクライアント メソッドを呼び出す

ハブからメッセージを受信するには、 の[on](/javascript/api/%40aspnet/signalr/hubconnection#on)メソッドを使用して`HubConnection`メソッドを定義します。

* JavaScript クライアント メソッドの名前。 次の例では、メソッド名は`ReceiveMessage`です。
* ハブがメソッドに渡す引数。 次の例では、引数値は`message`です。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

上記のコードは、`connection.on`サーバー側コードが[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッドを使用して呼び出したときに実行されます。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRで定義されたメソッド名と引数を一致させることによって、どのクライアント`SendAsync``connection.on`メソッドを呼び出すかを決定します。

> [!NOTE]
> ベスト プラクティスとして、after`on`の[start](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection`メソッドを呼び出します。 これにより、メッセージを受信する前にハンドラーが登録されます。

## <a name="error-handling-and-logging"></a>エラー処理とログ記録

メソッドを`catch`メソッドの最後にチェイン`start`して、クライアント側のエラーを処理します。 ブラウザ`console.error`のコンソールにエラーを出力するために使用します。

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

接続が確立されたときにログに記録するロガーとイベントの種類を渡すことによって、クライアント側のログ トレースをセットアップします。 メッセージは、指定したログ レベル以上でログに記録されます。 使用可能なログ・レベルは次のとおりです。

* `signalR.LogLevel.Error`&ndash;エラー メッセージ。 メッセージ`Error`のみをログに記録します。
* `signalR.LogLevel.Warning`&ndash;潜在的なエラーに関する警告メッセージ。 ログ`Warning`、`Error`およびメッセージ。
* `signalR.LogLevel.Information`&ndash;エラーのないステータス メッセージ。 ログ`Information` `Warning`、、`Error`およびメッセージ。
* `signalR.LogLevel.Trace`&ndash;メッセージをトレースします。 ハブとクライアントの間で転送されるデータを含め、すべてをログに記録します。

ログ レベルを構成するには[、ハブ接続ビルダー](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)の[ログの構成](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。 メッセージはブラウザコンソールに記録されます。

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>クライアントの再接続

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自動的に再接続

の JavaScriptSignalRクライアントは、[ハブコネクション ビルダー](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)の`withAutomaticReconnect`メソッドを使用して自動的に再接続するように構成できます。 既定では、自動的に再接続されません。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

パラメータを指定せずに`withAutomaticReconnect()`、各再接続を試行する前にそれぞれ 0、2、10、および 30 秒待機するようにクライアントを設定し、4 回の試行に失敗した後に停止します。

再接続の試行を開始する前に`HubConnection`、状態に遷`HubConnectionState.Reconnecting`移`onreconnecting`し、`Disconnected`状態に遷移して、自動再接続を設定せずにコールバックを`onclose`トリガーする代わりに`HubConnection`、コールバックを起動します。 これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にする機会が得られます。

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

クライアントが最初の 4 回の試行で正常に再接続した`HubConnection`場合、 は`Connected`状態に戻り、`onreconnected`コールバックを起動します。 これにより、接続が再確立されたことをユーザーに通知できます。

接続はサーバーにまったく新しく表示されるため、コールバックに`connectionId`新しい情報が`onreconnected`提供されます。

> [!WARNING]
> ネゴシエーション`onreconnected`をスキップするように`connectionId`設定されている場合、コールバックの`HubConnection`パラメータは未定義[skip negotiation](xref:signalr/configuration#configure-client-options)になります。

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`初期開始エラーを`HubConnection`再試行するように構成されないので、開始エラーは手動で処理する必要があります。

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

クライアントが最初の 4 回の試行で正常に再接続しない場合は`HubConnection`、`Disconnected`状態に遷移し[、onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックを起動します。 これにより、接続が完全に失われたことをユーザーに通知し、ページを更新することをお勧めします。

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

再接続のタイミングを切断または変更する前に、カスタムの再接続試行回数を設定するために、`withAutomaticReconnect`各再接続の試行を開始するまでの待機時間をミリ秒単位で表す番号の配列を受け入れます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

上記の例では、接続`HubConnection`が切断された直後に再接続を開始するように を構成しています。 これは、デフォルトの構成にも当てはまります。

最初の再接続が失敗した場合、2 回目の再接続も、既定の構成と同じように 2 秒待つのではなく、すぐに開始されます。

2 回目の再接続が失敗すると、3 回目の再接続は 10 秒後に開始され、再びデフォルトの構成と同じになります。

カスタム動作は、3 回目の再接続の失敗後に停止し、既定の設定のように 30 秒後に再接続を再試行するのではなく、既定の動作と再び異なります。

自動再接続の試行回数をさらに制御する場合は、`withAutomaticReconnect`という単`IRetryPolicy``nextRetryDelayInMilliseconds`一のメソッドを持つインターフェイスを実装するオブジェクトを受け入れます。

`nextRetryDelayInMilliseconds`型`RetryContext`を指定して引数を 1 つ取ります。 には`RetryContext`、`previousRetryCount``elapsedMilliseconds`の 3`retryReason`つのプロパティがあり`number`、それぞれ`number`、`Error`と が 1 つになります。 最初の再接続が試行される前`previousRetryCount`に`elapsedMilliseconds`、両方ともゼロになり、`retryReason`接続が失われる原因となったエラーになります。 再試行が失敗するたびに 1`previousRetryCount`ずつ増加し、`elapsedMilliseconds`それまでの再接続にかかった時間をミリ秒単位で反映するように更新され、最後の`retryReason`再接続の失敗の原因となった Error が返されます。

`nextRetryDelayInMilliseconds`次の再接続が試行されるまでの待機時間をミリ秒単位で表す数値を返すか`null`、再接続を`HubConnection`停止する必要があるかのいずれかを返す必要があります。

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

代わりに、「手動で再接続する」で示すように、クライアントを[手動で再接続](#manually-reconnect)するコードを記述することもできます。

::: moniker-end

### <a name="manually-reconnect"></a>手動で再接続する

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3.0 より前のバージョンでは、JavaScriptSignalRクライアントは自動的に再接続されません。 クライアントを手動で再接続するコードを記述する必要があります。

::: moniker-end

次のコードは、一般的な手動再接続の方法を示しています。

1. 接続を開始する関数 (この`start`場合は関数) が作成されます。
1. 接続の`start``onclose`イベント ハンドラーで関数を呼び出します。

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

実際の実装では、指数バックオフを使用するか、指定された回数再試行してから、あきらめます。

## <a name="additional-resources"></a>その他のリソース

* [JavaScript API リファレンス](/javascript/api/?view=signalr-js-latest)
* [Java スクリプトのチュートリアル](xref:tutorials/signalr)
* [ウェブパックとタイプスクリプトのチュートリアル](xref:tutorials/signalr-typescript-webpack)
* [ハブ](xref:signalr/hubs)
* [.NET クライアント](xref:signalr/dotnet-client)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
* [クロスオリジンリクエスト(CORS)](xref:security/cors)
* [AzureSignalRサービス サーバーレス ドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)
