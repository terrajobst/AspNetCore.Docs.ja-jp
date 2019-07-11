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
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="cc79c-103">ASP.NET Core SignalR JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="cc79c-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="cc79c-104">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="cc79c-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="cc79c-105">ASP.NET Core SignalR JavaScript クライアント ライブラリでは、サーバー側ハブのコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="cc79c-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cc79c-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="cc79c-107">SignalR クライアント パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="cc79c-107">Install the SignalR client package</span></span>

<span data-ttu-id="cc79c-108">SignalR JavaScript クライアント ライブラリとして提供される、 [npm](https://www.npmjs.com/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="cc79c-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="cc79c-109">Visual Studio を使用している場合は、実行`npm install`から、**パッケージ マネージャー コンソール**ルート フォルダー内で。</span><span class="sxs-lookup"><span data-stu-id="cc79c-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="cc79c-110">Visual Studio Code でからのコマンドを実行、**統合ターミナル**します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="cc79c-111">npm のインストール パッケージの内容を *node_modules\\@aspnet\signalr\dist\browser* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="cc79c-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="cc79c-112">という名前の新しいフォルダーを作成する*signalr*下、 *wwwroot\\lib*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="cc79c-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="cc79c-113">コピー、 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="cc79c-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="cc79c-114">SignalR JavaScript クライアントを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="cc79c-115">SignalR JavaScript クライアントでの参照、`<script>`要素。</span><span class="sxs-lookup"><span data-stu-id="cc79c-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="cc79c-116">ハブへの接続します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-116">Connect to a hub</span></span>

<span data-ttu-id="cc79c-117">次のコードでは、作成し、接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="cc79c-118">ハブの名前は大文字小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="cc79c-119">クロス オリジンの接続</span><span class="sxs-lookup"><span data-stu-id="cc79c-119">Cross-origin connections</span></span>

<span data-ttu-id="cc79c-120">通常、ブラウザーでは、要求されたページと同じドメインから接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="cc79c-121">ただし、状況が別のドメインへの接続が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="cc79c-122">悪意のあるサイトが別のサイトから機密データを読み取ることを防ぐために[クロス オリジン接続](xref:security/cors)は既定で無効になります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="cc79c-123">クロス オリジン要求を許可することで有効にする、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="cc79c-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="cc79c-124">クライアントからのハブ メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="cc79c-124">Call hub methods from client</span></span>

<span data-ttu-id="cc79c-125">JavaScript クライアントは、ハブ経由でのパブリック メソッドを呼び出して、[呼び出す](/javascript/api/%40aspnet/signalr/hubconnection#invoke)のメソッド、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="cc79c-126">`invoke`メソッドは 2 つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="cc79c-127">ハブ メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="cc79c-127">The name of the hub method.</span></span> <span data-ttu-id="cc79c-128">次の例では、ハブのメソッド名は`SendMessage`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="cc79c-129">ハブ メソッドで定義されている引数。</span><span class="sxs-lookup"><span data-stu-id="cc79c-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="cc79c-130">次の例では引数名は`message`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-130">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="cc79c-131">コード例では、現在のバージョンの Internet Explorer を除くすべての主要ブラウザーでサポートされている矢印関数の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-131">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="cc79c-132">Azure SignalR サービスを使用している場合*サーバーレス モード*、クライアントからハブ メソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="cc79c-132">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="cc79c-133">詳細については、次を参照してください。、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-133">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="cc79c-134">`invoke`メソッドは、JavaScript を返します[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-134">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="cc79c-135">`Promise`解決戻り値 (ある場合) サーバー上のメソッドが返されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-135">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="cc79c-136">サーバー上のメソッドが、エラーをスローした場合、`Promise`と、エラー メッセージは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-136">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="cc79c-137">使用して、`then`と`catch`メソッド、`Promise`自体と、このような場合を処理するために (または`await`構文)。</span><span class="sxs-lookup"><span data-stu-id="cc79c-137">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="cc79c-138">`send`メソッドは、JavaScript を返します`Promise`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-138">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="cc79c-139">`Promise`サーバーに、メッセージが送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-139">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="cc79c-140">メッセージを送信中にエラーがある場合、`Promise`と、エラー メッセージは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-140">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="cc79c-141">使用して、`then`と`catch`メソッド、`Promise`自体と、このような場合を処理するために (または`await`構文)。</span><span class="sxs-lookup"><span data-stu-id="cc79c-141">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="cc79c-142">使用して`send`サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="cc79c-142">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="cc79c-143">その結果、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="cc79c-143">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="cc79c-144">ハブからのクライアント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="cc79c-144">Call client methods from hub</span></span>

<span data-ttu-id="cc79c-145">ハブからメッセージを受信する定義を使用して、メソッド、[で](/javascript/api/%40aspnet/signalr/hubconnection#on)のメソッド、`HubConnection`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-145">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="cc79c-146">JavaScript クライアント メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="cc79c-146">The name of the JavaScript client method.</span></span> <span data-ttu-id="cc79c-147">次の例では、メソッド名は`ReceiveMessage`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-147">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="cc79c-148">引数は、hub は、メソッドに渡します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-148">Arguments the hub passes to the method.</span></span> <span data-ttu-id="cc79c-149">引数の値は、次の例では、`message`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-149">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="cc79c-150">上記のコードで`connection.on`を使用してサーバー側コードから呼び出すときに実行される、 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッド。</span><span class="sxs-lookup"><span data-stu-id="cc79c-150">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="cc79c-151">SignalR を呼び出すメソッド名を照合することによってクライアントの方法を指定してで定義されている引数`SendAsync`と`connection.on`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-151">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="cc79c-152">ベスト プラクティスを呼び出して、[開始](/javascript/api/%40aspnet/signalr/hubconnection#start)メソッドを`HubConnection`後`on`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-152">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="cc79c-153">これにより、すべてのメッセージを受信する前に、ハンドラーが登録されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-153">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="cc79c-154">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="cc79c-154">Error handling and logging</span></span>

<span data-ttu-id="cc79c-155">チェーンを`catch`メソッドの末尾に、`start`クライアント側のエラーを処理するメソッド。</span><span class="sxs-lookup"><span data-stu-id="cc79c-155">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="cc79c-156">使用`console.error`ブラウザーのコンソールにエラーを出力します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-156">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="cc79c-157">接続が行われたときにログに記録するには、logger とイベントの種類を渡すことによって、クライアント側のログ トレースをセットアップします。</span><span class="sxs-lookup"><span data-stu-id="cc79c-157">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="cc79c-158">指定されたログ レベル以降、メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-158">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="cc79c-159">使用可能なログ レベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc79c-159">Available log levels are as follows:</span></span>

* <span data-ttu-id="cc79c-160">`signalR.LogLevel.Error` &ndash; エラー メッセージ。</span><span class="sxs-lookup"><span data-stu-id="cc79c-160">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="cc79c-161">ログ`Error`メッセージのみです。</span><span class="sxs-lookup"><span data-stu-id="cc79c-161">Logs `Error` messages only.</span></span>
* <span data-ttu-id="cc79c-162">`signalR.LogLevel.Warning` &ndash; 可能性のあるエラーについての警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="cc79c-162">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="cc79c-163">ログ`Warning`、および`Error`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="cc79c-163">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cc79c-164">`signalR.LogLevel.Information` &ndash; ステータス メッセージがエラーなし。</span><span class="sxs-lookup"><span data-stu-id="cc79c-164">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="cc79c-165">ログ`Information`、 `Warning`、および`Error`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="cc79c-165">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cc79c-166">`signalR.LogLevel.Trace` &ndash; メッセージをトレースします。</span><span class="sxs-lookup"><span data-stu-id="cc79c-166">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="cc79c-167">ハブとクライアント間で転送されるデータを含め、すべてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-167">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="cc79c-168">使用して、 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッド[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)ログ レベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-168">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="cc79c-169">メッセージは、ブラウザーのコンソールに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-169">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="cc79c-170">クライアントを再接続します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-170">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="cc79c-171">自動的に再接続します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-171">Automatically reconnect</span></span>

<span data-ttu-id="cc79c-172">使用して再接続が自動的に SignalR JavaScript クライアントを構成することができます、`withAutomaticReconnect`メソッド[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-172">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="cc79c-173">既定では、自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="cc79c-173">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="cc79c-174">任意のパラメーターを指定せず`withAutomaticReconnect()`後、4 つの失敗した試行を停止する各再接続の試行を試す前に、それぞれ 0、2、10、および 30 秒間待機するクライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-174">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="cc79c-175">再接続しようとすると、開始する前に、`HubConnection`への移行には、`HubConnectionState.Reconnecting`起動状態にあり、その`onreconnecting`コールバックへの移行ではなく、`Disconnected`状態とトリガーの`onclose`コールバックなどの`HubConnection`せず、自動再接続を構成します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-175">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="cc79c-176">これは、UI 要素を無効にして、接続が失われたことをユーザーに警告する機会を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-176">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="cc79c-177">内の最初の 4 つの試行で、クライアントが再接続に成功した場合、`HubConnection`から戻るへの移行、`Connected`起動状態にあり、その`onreconnected`コールバック。</span><span class="sxs-lookup"><span data-stu-id="cc79c-177">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="cc79c-178">これは、接続が再確立されたユーザーに通知する機会を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-178">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="cc79c-179">接続は、サーバーから完全に新規に見えるため、新しい`connectionId`に提供される、`onreconnected`コールバック。</span><span class="sxs-lookup"><span data-stu-id="cc79c-179">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="cc79c-180">`onreconnected`コールバックの`connectionId`パラメーターは不確定になる場合、`HubConnection`するように構成された[ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-180">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="cc79c-181">`withAutomaticReconnect()` 構成はありません、`HubConnection`起動障害を手動で処理する必要があるために、初回起動時の障害を再試行します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-181">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="cc79c-182">内の最初の 4 つの試行で、クライアントが再接続が正常に場合、`HubConnection`への移行には、`Disconnected`起動状態にあり、その[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバック。</span><span class="sxs-lookup"><span data-stu-id="cc79c-182">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="cc79c-183">これは、接続が完全に失われ、ページの更新をお勧めします。 ユーザーに通知する機会を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-183">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="cc79c-184">切断する前に再接続試行のカスタムの数を構成または再接続のタイミングを変更するには`withAutomaticReconnect`再接続が試みられるたびに開始する前に待機するミリ秒の遅延を表す数値の配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-184">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="cc79c-185">上記の例では、`HubConnection`接続が失われた後にすぐに再接続しようとすると開始します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-185">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="cc79c-186">またこれは、既定の構成に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-186">This is also true for the default configuration.</span></span>

<span data-ttu-id="cc79c-187">既定の構成でこれと同じように、2 秒待機しているのではなく、最初の再接続試行が失敗すると、2 回目の再接続試行がすぐに開始もは。</span><span class="sxs-lookup"><span data-stu-id="cc79c-187">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="cc79c-188">2 回目の再接続の試行が失敗した場合、これは、既定の構成と同様にもう一度、10 秒後に、3 番目の再接続の試行が開始されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-188">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="cc79c-189">カスタム動作し、もう一度ブロックから分化既定の動作を停止して 3 番目の再接続試行のいずれかの代わりに失敗した後は別の 30 秒後、既定の構成のように試行を再接続します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-189">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="cc79c-190">タイミングと自動の数をさらに多くのコントロールの接続試行、なら`withAutomaticReconnect`実装するオブジェクトを受け入れる、`IReconnectPolicy`インターフェイスという単一のメソッドを`nextRetryDelayInMilliseconds`します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-190">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IReconnectPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="cc79c-191">`nextRetryDelayInMilliseconds` 2 つの引数を受け取り`previousRetryCount`と`elapsedMilliseconds`、両方の数値であります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-191">`nextRetryDelayInMilliseconds` takes two arguments, `previousRetryCount` and `elapsedMilliseconds`, which are both numbers.</span></span> <span data-ttu-id="cc79c-192">最初の再接続試行する前に両方`previousRetryCount`と`elapsedMilliseconds`は 0 になります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-192">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero.</span></span> <span data-ttu-id="cc79c-193">各障害が発生した再試行後`previousRetryCount`1 つずつ増えますと`elapsedMilliseconds`はこれまでに再接続するミリ秒単位で費やされた時間数を反映するように更新されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-193">After each failed retry attempt, `previousRetryCount` will be incremented by one and `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds.</span></span>

<span data-ttu-id="cc79c-194">`nextRetryDelayInMilliseconds` いずれかを表す数値ミリ秒数、[次へ] の再接続の試行までの待機を返す必要がありますまたは`null`場合、`HubConnection`再接続を停止する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-194">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="cc79c-195">再接続で示すように手動で、クライアント コードを記述する代わりに、[手動で再接続](#manually-reconnect)します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-195">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="cc79c-196">手動での再接続します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-196">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="cc79c-197">3\.0 では、前に SignalR JavaScript クライアントは自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="cc79c-197">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="cc79c-198">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc79c-198">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="cc79c-199">次のコードでは、手動による再接続の一般的なアプローチを示します。</span><span class="sxs-lookup"><span data-stu-id="cc79c-199">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="cc79c-200">関数 (ここで、`start`関数)、接続を開始するが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cc79c-200">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="cc79c-201">呼び出す、`start`関数で、接続の`onclose`イベント ハンドラー。</span><span class="sxs-lookup"><span data-stu-id="cc79c-201">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="cc79c-202">実際の実装は、指数バックオフを使用して、または断念する前に指定された回数を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="cc79c-202">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc79c-203">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cc79c-203">Additional resources</span></span>

* [<span data-ttu-id="cc79c-204">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="cc79c-204">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="cc79c-205">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="cc79c-205">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="cc79c-206">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="cc79c-206">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="cc79c-207">ハブ</span><span class="sxs-lookup"><span data-stu-id="cc79c-207">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="cc79c-208">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="cc79c-208">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="cc79c-209">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="cc79c-209">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="cc79c-210">クロス オリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="cc79c-210">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="cc79c-211">Azure SignalR サービスのサーバーレス ドキュメント</span><span class="sxs-lookup"><span data-stu-id="cc79c-211">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
