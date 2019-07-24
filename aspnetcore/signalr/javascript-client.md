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
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="7be09-103">ASP.NET Core SignalR JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="7be09-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="7be09-104">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="7be09-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="7be09-105">ASP.NET Core SignalR JavaScript クライアント ライブラリでは、サーバー側ハブのコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="7be09-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="7be09-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7be09-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="7be09-107">SignalR クライアント パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7be09-107">Install the SignalR client package</span></span>

<span data-ttu-id="7be09-108">SignalR JavaScript クライアント ライブラリとして提供される、 [npm](https://www.npmjs.com/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="7be09-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="7be09-109">Visual Studio を使用している場合は、実行`npm install`から、**パッケージ マネージャー コンソール**ルート フォルダー内で。</span><span class="sxs-lookup"><span data-stu-id="7be09-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="7be09-110">Visual Studio Code でからのコマンドを実行、**統合ターミナル**します。</span><span class="sxs-lookup"><span data-stu-id="7be09-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

<span data-ttu-id="7be09-111">npm のインストール パッケージの内容を *node_modules\\@microsoft\signalr\dist\browser* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="7be09-111">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="7be09-112">という名前の新しいフォルダーを作成する*signalr*下、 *wwwroot\\lib*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="7be09-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="7be09-113">コピー、 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="7be09-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="7be09-114">npm のインストール パッケージの内容を *node_modules\\@aspnet\signalr\dist\browser* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="7be09-114">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="7be09-115">という名前の新しいフォルダーを作成する*signalr*下、 *wwwroot\\lib*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="7be09-115">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="7be09-116">コピー、 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="7be09-116">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="7be09-117">SignalR JavaScript クライアントを使用します。</span><span class="sxs-lookup"><span data-stu-id="7be09-117">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="7be09-118">SignalR JavaScript クライアントでの参照、`<script>`要素。</span><span class="sxs-lookup"><span data-stu-id="7be09-118">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="7be09-119">ハブへの接続します。</span><span class="sxs-lookup"><span data-stu-id="7be09-119">Connect to a hub</span></span>

<span data-ttu-id="7be09-120">次のコードでは、作成し、接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="7be09-120">The following code creates and starts a connection.</span></span> <span data-ttu-id="7be09-121">ハブの名前は大文字小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="7be09-121">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="7be09-122">クロス オリジンの接続</span><span class="sxs-lookup"><span data-stu-id="7be09-122">Cross-origin connections</span></span>

<span data-ttu-id="7be09-123">通常、ブラウザーでは、要求されたページと同じドメインから接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7be09-123">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="7be09-124">ただし、状況が別のドメインへの接続が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="7be09-124">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="7be09-125">悪意のあるサイトが別のサイトから機密データを読み取ることを防ぐために[クロス オリジン接続](xref:security/cors)は既定で無効になります。</span><span class="sxs-lookup"><span data-stu-id="7be09-125">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="7be09-126">クロス オリジン要求を許可することで有効にする、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="7be09-126">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7be09-127">クライアントからのハブ メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="7be09-127">Call hub methods from client</span></span>

<span data-ttu-id="7be09-128">JavaScript クライアントは、ハブ経由でのパブリック メソッドを呼び出して、[呼び出す](/javascript/api/%40aspnet/signalr/hubconnection#invoke)のメソッド、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)します。</span><span class="sxs-lookup"><span data-stu-id="7be09-128">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="7be09-129">`invoke`メソッドは 2 つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7be09-129">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="7be09-130">ハブ メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="7be09-130">The name of the hub method.</span></span> <span data-ttu-id="7be09-131">次の例では、ハブのメソッド名は`SendMessage`します。</span><span class="sxs-lookup"><span data-stu-id="7be09-131">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="7be09-132">ハブ メソッドで定義されている引数。</span><span class="sxs-lookup"><span data-stu-id="7be09-132">Any arguments defined in the hub method.</span></span> <span data-ttu-id="7be09-133">次の例では引数名は`message`します。</span><span class="sxs-lookup"><span data-stu-id="7be09-133">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="7be09-134">このコード例では、Internet Explorer を除くすべての主要なブラウザーの現在のバージョンでサポートされている、矢印関数の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="7be09-134">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="7be09-135">*サーバーレスモード*で Azure SignalR サービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="7be09-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="7be09-136">詳細については、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7be09-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="7be09-137">メソッド`invoke`は、JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="7be09-137">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="7be09-138">は`Promise` 、サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-138">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="7be09-139">サーバー上のメソッドがエラー `Promise`をスローした場合、はエラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-139">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7be09-140">これらのケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。`then`</span><span class="sxs-lookup"><span data-stu-id="7be09-140">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="7be09-141">メソッド`send`は、JavaScript `Promise`を返します。</span><span class="sxs-lookup"><span data-stu-id="7be09-141">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="7be09-142">は`Promise` 、メッセージがサーバーに送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-142">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="7be09-143">メッセージの送信中にエラーが発生した`Promise`場合、はエラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-143">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7be09-144">これらのケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。`then`</span><span class="sxs-lookup"><span data-stu-id="7be09-144">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="7be09-145">を`send`使用すると、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="7be09-145">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="7be09-146">このため、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="7be09-146">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7be09-147">ハブからのクライアント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="7be09-147">Call client methods from hub</span></span>

<span data-ttu-id="7be09-148">ハブからメッセージを受信する定義を使用して、メソッド、[で](/javascript/api/%40aspnet/signalr/hubconnection#on)のメソッド、`HubConnection`します。</span><span class="sxs-lookup"><span data-stu-id="7be09-148">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="7be09-149">JavaScript クライアント メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="7be09-149">The name of the JavaScript client method.</span></span> <span data-ttu-id="7be09-150">次の例では、メソッド名は`ReceiveMessage`します。</span><span class="sxs-lookup"><span data-stu-id="7be09-150">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="7be09-151">引数は、hub は、メソッドに渡します。</span><span class="sxs-lookup"><span data-stu-id="7be09-151">Arguments the hub passes to the method.</span></span> <span data-ttu-id="7be09-152">引数の値は、次の例では、`message`します。</span><span class="sxs-lookup"><span data-stu-id="7be09-152">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="7be09-153">上記のコードで`connection.on`を使用してサーバー側コードから呼び出すときに実行される、 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッド。</span><span class="sxs-lookup"><span data-stu-id="7be09-153">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="7be09-154">SignalR を呼び出すメソッド名を照合することによってクライアントの方法を指定してで定義されている引数`SendAsync`と`connection.on`します。</span><span class="sxs-lookup"><span data-stu-id="7be09-154">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="7be09-155">ベスト プラクティスを呼び出して、[開始](/javascript/api/%40aspnet/signalr/hubconnection#start)メソッドを`HubConnection`後`on`します。</span><span class="sxs-lookup"><span data-stu-id="7be09-155">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="7be09-156">これにより、すべてのメッセージを受信する前に、ハンドラーが登録されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-156">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="7be09-157">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="7be09-157">Error handling and logging</span></span>

<span data-ttu-id="7be09-158">チェーンを`catch`メソッドの末尾に、`start`クライアント側のエラーを処理するメソッド。</span><span class="sxs-lookup"><span data-stu-id="7be09-158">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="7be09-159">使用`console.error`ブラウザーのコンソールにエラーを出力します。</span><span class="sxs-lookup"><span data-stu-id="7be09-159">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="7be09-160">接続が行われたときにログに記録するには、logger とイベントの種類を渡すことによって、クライアント側のログ トレースをセットアップします。</span><span class="sxs-lookup"><span data-stu-id="7be09-160">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="7be09-161">指定されたログ レベル以降、メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-161">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="7be09-162">使用可能なログ レベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7be09-162">Available log levels are as follows:</span></span>

* <span data-ttu-id="7be09-163">`signalR.LogLevel.Error` &ndash; エラー メッセージ。</span><span class="sxs-lookup"><span data-stu-id="7be09-163">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="7be09-164">ログ`Error`メッセージのみです。</span><span class="sxs-lookup"><span data-stu-id="7be09-164">Logs `Error` messages only.</span></span>
* <span data-ttu-id="7be09-165">`signalR.LogLevel.Warning` &ndash; 可能性のあるエラーについての警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="7be09-165">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="7be09-166">ログ`Warning`、および`Error`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="7be09-166">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7be09-167">`signalR.LogLevel.Information` &ndash; ステータス メッセージがエラーなし。</span><span class="sxs-lookup"><span data-stu-id="7be09-167">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="7be09-168">ログ`Information`、 `Warning`、および`Error`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="7be09-168">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7be09-169">`signalR.LogLevel.Trace` &ndash; メッセージをトレースします。</span><span class="sxs-lookup"><span data-stu-id="7be09-169">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="7be09-170">ハブとクライアント間で転送されるデータを含め、すべてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="7be09-170">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="7be09-171">使用して、 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッド[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)ログ レベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="7be09-171">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="7be09-172">メッセージは、ブラウザーのコンソールに記録されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-172">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="7be09-173">クライアントを再接続します。</span><span class="sxs-lookup"><span data-stu-id="7be09-173">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="7be09-174">自動的に再接続する</span><span class="sxs-lookup"><span data-stu-id="7be09-174">Automatically reconnect</span></span>

<span data-ttu-id="7be09-175">SignalR の JavaScript クライアントは、 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)のメソッドを使用して自動的に再接続するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="7be09-175">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="7be09-176">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="7be09-176">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="7be09-177">パラメーターを指定し`withAutomaticReconnect()`ない場合、は、再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。</span><span class="sxs-lookup"><span data-stu-id="7be09-177">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="7be09-178">`HubConnection`再接続`onreconnecting` `HubConnectionState.Reconnecting` の試行`Disconnected`を開始する前に、は状態に遷移し、コールバックを起動します。状態に遷移し、次のようにコールバックをトリガーするのではなく、`onclose` `HubConnection`自動再接続が構成されていません。</span><span class="sxs-lookup"><span data-stu-id="7be09-178">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="7be09-179">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="7be09-179">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7be09-180">最初の4回の試行でクライアントが正常に再`HubConnection`接続した場合、 `Connected`は状態に戻り`onreconnected` 、コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="7be09-180">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="7be09-181">これにより、接続が再確立されたことをユーザーに通知することができます。</span><span class="sxs-lookup"><span data-stu-id="7be09-181">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="7be09-182">接続はサーバーにまったく新しいものであるため、 `connectionId` `onreconnected`コールバックに新しいが提供されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-182">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="7be09-183">が`onreconnected` [ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成さ`HubConnection`れている場合、コールバックの`connectionId`パラメーターは未定義になります。</span><span class="sxs-lookup"><span data-stu-id="7be09-183">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7be09-184">`withAutomaticReconnect()`最初の開始`HubConnection`エラーを再試行するようにを構成しません。そのため、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7be09-184">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="7be09-185">最初の4回の試行でクライアントが正常に再接続`HubConnection`されない場合`Disconnected` 、は状態に遷移し、その[閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="7be09-185">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="7be09-186">これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7be09-186">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7be09-187">再接続のタイミングを切断または変更する前に、カスタムの再接続試行`withAutomaticReconnect`回数を構成するために、では、各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7be09-187">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="7be09-188">前の例では`HubConnection` 、接続が失われた直後に再接続を開始するようにを構成しています。</span><span class="sxs-lookup"><span data-stu-id="7be09-188">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="7be09-189">これは、既定の構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="7be09-189">This is also true for the default configuration.</span></span>

<span data-ttu-id="7be09-190">最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-190">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="7be09-191">2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。</span><span class="sxs-lookup"><span data-stu-id="7be09-191">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="7be09-192">このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。</span><span class="sxs-lookup"><span data-stu-id="7be09-192">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="7be09-193">タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `withAutomaticReconnect` `IRetryPolicy`インターフェイスを実装するオブジェクトを受け取ります。このインターフェイスには`nextRetryDelayInMilliseconds`、という名前の1つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="7be09-193">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="7be09-194">`nextRetryDelayInMilliseconds`型`RetryContext`の1つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7be09-194">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="7be09-195">`retryReason` `elapsedMilliseconds` `number` `previousRetryCount` `number` には`Error` 、、、およびの3つのプロパティがあります。`RetryContext`</span><span class="sxs-lookup"><span data-stu-id="7be09-195">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="7be09-196">最初の再接続を試行する`previousRetryCount`前`elapsedMilliseconds`に、との`retryReason`両方がゼロになり、は接続が失われる原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="7be09-196">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="7be09-197">再試行が再試行されるたび`previousRetryCount`に、が1ずつインクリメント`elapsedMilliseconds`され、これまでの再接続にかかった時間 (ミリ秒単位) を`retryReason`反映して更新されます。また、は、最後に再接続を試みたときに発生したエラーになります。オーバー.</span><span class="sxs-lookup"><span data-stu-id="7be09-197">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="7be09-198">`nextRetryDelayInMilliseconds`は、次回の再接続が試行される前に待機するミリ秒数を`null`表す数値`HubConnection`を返すか、再接続を停止する必要がある場合はを返します。</span><span class="sxs-lookup"><span data-stu-id="7be09-198">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="7be09-199">または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="7be09-199">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="7be09-200">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="7be09-200">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="7be09-201">3\.0 より前では、SignalR の JavaScript クライアントは自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="7be09-201">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="7be09-202">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7be09-202">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="7be09-203">次のコードは、標準的な手動再接続アプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="7be09-203">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="7be09-204">関数 (ここで、`start`関数)、接続を開始するが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7be09-204">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="7be09-205">呼び出す、`start`関数で、接続の`onclose`イベント ハンドラー。</span><span class="sxs-lookup"><span data-stu-id="7be09-205">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="7be09-206">実際の実装は、指数バックオフを使用して、または断念する前に指定された回数を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="7be09-206">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7be09-207">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7be09-207">Additional resources</span></span>

* [<span data-ttu-id="7be09-208">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="7be09-208">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="7be09-209">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="7be09-209">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7be09-210">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="7be09-210">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="7be09-211">ハブ</span><span class="sxs-lookup"><span data-stu-id="7be09-211">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7be09-212">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="7be09-212">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7be09-213">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="7be09-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="7be09-214">クロス オリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="7be09-214">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="7be09-215">Azure SignalR Service サーバーレスドキュメント</span><span class="sxs-lookup"><span data-stu-id="7be09-215">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
