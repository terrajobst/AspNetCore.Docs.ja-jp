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
ms.openlocfilehash: 926160a41c82853d83890f0d52b14d7d5561a990
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963767"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="17f60-103">ASP.NET Core SignalR JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="17f60-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="17f60-104">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="17f60-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="17f60-105">ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="17f60-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="17f60-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="17f60-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="17f60-107">SignalR クライアントパッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="17f60-107">Install the SignalR client package</span></span>

<span data-ttu-id="17f60-108">SignalR JavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="17f60-109">Visual Studio を使用している場合は、ルートフォルダー内の**パッケージマネージャーコンソール**から `npm install` を実行します。</span><span class="sxs-lookup"><span data-stu-id="17f60-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="17f60-110">Visual Studio Code には、**統合ターミナル**からコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="17f60-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

<span data-ttu-id="17f60-111">npm は、 *node_modules\\@microsoft\signalr\dist\browser* フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="17f60-111">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="17f60-112">*Wwwroot\\lib*フォルダーの下に、 *signalr*という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="17f60-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="17f60-113">*Signalr*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="17f60-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="17f60-114">npm は、 *node_modules\\@aspnet\signalr\dist\browser* フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="17f60-114">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="17f60-115">*Wwwroot\\lib*フォルダーの下に、 *signalr*という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="17f60-115">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="17f60-116">*Signalr*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="17f60-116">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a><span data-ttu-id="17f60-117">SignalR JavaScript クライアントを使用する</span><span class="sxs-lookup"><span data-stu-id="17f60-117">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="17f60-118">`<script>` 要素で SignalR JavaScript クライアントを参照します。</span><span class="sxs-lookup"><span data-stu-id="17f60-118">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="17f60-119">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="17f60-119">Connect to a hub</span></span>

<span data-ttu-id="17f60-120">次のコードでは、接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="17f60-120">The following code creates and starts a connection.</span></span> <span data-ttu-id="17f60-121">ハブの名前は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="17f60-121">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="17f60-122">クロスオリジン接続</span><span class="sxs-lookup"><span data-stu-id="17f60-122">Cross-origin connections</span></span>

<span data-ttu-id="17f60-123">通常、ブラウザーは、要求されたページと同じドメインからの接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="17f60-123">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="17f60-124">ただし、別のドメインへの接続が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="17f60-124">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="17f60-125">悪意のあるサイトが別のサイトから機密データを読み取れないようにするため、既定では、[クロスオリジン接続](xref:security/cors)は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="17f60-125">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="17f60-126">クロスオリジン要求を許可するには、`Startup` クラスで有効にします。</span><span class="sxs-lookup"><span data-stu-id="17f60-126">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="17f60-127">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="17f60-127">Call hub methods from client</span></span>

<span data-ttu-id="17f60-128">JavaScript クライアントは、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)の[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)メソッドを使用して、ハブでパブリックメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="17f60-128">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="17f60-129">`invoke` メソッドは、次の2つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="17f60-129">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="17f60-130">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="17f60-130">The name of the hub method.</span></span> <span data-ttu-id="17f60-131">次の例では、ハブのメソッド名が `SendMessage`ます。</span><span class="sxs-lookup"><span data-stu-id="17f60-131">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="17f60-132">ハブメソッドで定義されているすべての引数。</span><span class="sxs-lookup"><span data-stu-id="17f60-132">Any arguments defined in the hub method.</span></span> <span data-ttu-id="17f60-133">次の例では、引数名は `message`です。</span><span class="sxs-lookup"><span data-stu-id="17f60-133">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="17f60-134">このコード例では、Internet Explorer を除くすべての主要なブラウザーの現在のバージョンでサポートされている、矢印関数の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="17f60-134">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="17f60-135">*サーバーレスモード*で Azure SignalR サービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="17f60-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="17f60-136">詳細については、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17f60-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="17f60-137">`invoke` メソッドは、JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="17f60-137">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="17f60-138">`Promise` は、サーバーのメソッドがを返すときに戻り値 (存在する場合) で解決されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-138">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="17f60-139">サーバー上のメソッドがエラーをスローした場合、`Promise` はエラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-139">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="17f60-140">これらのケース (または `await` 構文) を処理するには、`Promise` 自体で `then` および `catch` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="17f60-140">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="17f60-141">`send` メソッドは、JavaScript `Promise`を返します。</span><span class="sxs-lookup"><span data-stu-id="17f60-141">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="17f60-142">`Promise` は、メッセージがサーバーに送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-142">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="17f60-143">メッセージの送信中にエラーが発生した場合、`Promise` はエラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-143">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="17f60-144">これらのケース (または `await` 構文) を処理するには、`Promise` 自体で `then` および `catch` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="17f60-144">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="17f60-145">`send` を使用すると、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="17f60-145">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="17f60-146">このため、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="17f60-146">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="17f60-147">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="17f60-147">Call client methods from hub</span></span>

<span data-ttu-id="17f60-148">ハブからメッセージを受信するには、`HubConnection`の[on](/javascript/api/%40aspnet/signalr/hubconnection#on)メソッドを使用してメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="17f60-148">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="17f60-149">JavaScript クライアントメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="17f60-149">The name of the JavaScript client method.</span></span> <span data-ttu-id="17f60-150">次の例では、メソッド名は `ReceiveMessage`です。</span><span class="sxs-lookup"><span data-stu-id="17f60-150">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="17f60-151">ハブがメソッドに渡す引数。</span><span class="sxs-lookup"><span data-stu-id="17f60-151">Arguments the hub passes to the method.</span></span> <span data-ttu-id="17f60-152">次の例では、引数の値は `message`です。</span><span class="sxs-lookup"><span data-stu-id="17f60-152">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="17f60-153">`connection.on` の前のコードは、サーバー側のコードが[Sendasync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッドを使用して呼び出したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-153">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="17f60-154"> は、`SendAsync` と `connection.on`で定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定します。</span><span class="sxs-lookup"><span data-stu-id="17f60-154"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="17f60-155">ベストプラクティスとして、`on`後に `HubConnection` で[start](/javascript/api/%40aspnet/signalr/hubconnection#start)メソッドを呼び出すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="17f60-155">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="17f60-156">これにより、メッセージを受信する前にハンドラーが確実に登録されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-156">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="17f60-157">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="17f60-157">Error handling and logging</span></span>

<span data-ttu-id="17f60-158">`catch` メソッドを `start` メソッドの最後にチェーンして、クライアント側のエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="17f60-158">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="17f60-159">`console.error` を使用して、ブラウザーのコンソールにエラーを出力します。</span><span class="sxs-lookup"><span data-stu-id="17f60-159">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="17f60-160">接続が確立されたときにログに記録する logger と種類のイベントを渡すことによって、クライアント側のログトレースを設定します。</span><span class="sxs-lookup"><span data-stu-id="17f60-160">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="17f60-161">メッセージは、指定されたログレベル以上でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-161">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="17f60-162">使用可能なログレベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="17f60-162">Available log levels are as follows:</span></span>

* <span data-ttu-id="17f60-163">&ndash; エラーメッセージを `signalR.LogLevel.Error` します。</span><span class="sxs-lookup"><span data-stu-id="17f60-163">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="17f60-164">`Error` メッセージのみをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="17f60-164">Logs `Error` messages only.</span></span>
* <span data-ttu-id="17f60-165">潜在的なエラーに関する警告メッセージを `signalR.LogLevel.Warning` &ndash; ます。</span><span class="sxs-lookup"><span data-stu-id="17f60-165">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="17f60-166">`Warning`メッセージと `Error` メッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="17f60-166">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="17f60-167">エラーのない &ndash; ステータスメッセージを `signalR.LogLevel.Information` します。</span><span class="sxs-lookup"><span data-stu-id="17f60-167">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="17f60-168">`Information`、`Warning`、および `Error` メッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="17f60-168">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="17f60-169">トレースメッセージを `signalR.LogLevel.Trace` &ndash; ます。</span><span class="sxs-lookup"><span data-stu-id="17f60-169">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="17f60-170">ハブとクライアント間で転送されたデータを含む、すべてをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="17f60-170">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="17f60-171">ログレベルを構成するには、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)で[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="17f60-171">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="17f60-172">メッセージは、ブラウザーコンソールに記録されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-172">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="17f60-173">クライアントの再接続</span><span class="sxs-lookup"><span data-stu-id="17f60-173">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="17f60-174">自動的に再接続する</span><span class="sxs-lookup"><span data-stu-id="17f60-174">Automatically reconnect</span></span>

<span data-ttu-id="17f60-175">SignalR の JavaScript クライアントは、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)の `withAutomaticReconnect` メソッドを使用して自動的に再接続するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="17f60-175">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="17f60-176">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="17f60-176">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="17f60-177">パラメーターを指定しない場合、`withAutomaticReconnect()` は、再接続を試行する前にそれぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。</span><span class="sxs-lookup"><span data-stu-id="17f60-177">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="17f60-178">再接続を試行する前に、`HubConnection` は `HubConnectionState.Reconnecting` 状態に遷移し、`onreconnecting` コールバックを起動します。これは、`Disconnected` 状態に遷移し、自動再接続が構成されていない `onclose` のような `HubConnection` コールバックをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="17f60-178">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="17f60-179">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="17f60-179">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="17f60-180">最初の4回の試行でクライアントが正常に再接続した場合、`HubConnection` は `Connected` 状態に戻り、`onreconnected` コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="17f60-180">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="17f60-181">これにより、接続が再確立されたことをユーザーに通知することができます。</span><span class="sxs-lookup"><span data-stu-id="17f60-181">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="17f60-182">接続はサーバーにまったく新しいものであるため、`onreconnected` コールバックに新しい `connectionId` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-182">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="17f60-183">`HubConnection` が[ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成されている場合、`onreconnected` コールバックの `connectionId` パラメーターは未定義になります。</span><span class="sxs-lookup"><span data-stu-id="17f60-183">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="17f60-184">`withAutomaticReconnect()` は、最初の開始エラーを再試行するように `HubConnection` を構成しません。そのため、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17f60-184">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="17f60-185">最初の4回の試行でクライアントが正常に再接続されなかった場合、`HubConnection` は `Disconnected` 状態に遷移し、その[閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックが発生します。</span><span class="sxs-lookup"><span data-stu-id="17f60-185">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="17f60-186">これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="17f60-186">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="17f60-187">再接続のタイミングを切断または変更する前に、カスタムの再接続試行回数を構成するために、`withAutomaticReconnect` は、各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="17f60-187">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="17f60-188">前の例では、接続が失われた直後に再接続を開始するように `HubConnection` を構成しています。</span><span class="sxs-lookup"><span data-stu-id="17f60-188">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="17f60-189">これは、既定の構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="17f60-189">This is also true for the default configuration.</span></span>

<span data-ttu-id="17f60-190">最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-190">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="17f60-191">2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。</span><span class="sxs-lookup"><span data-stu-id="17f60-191">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="17f60-192">このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。</span><span class="sxs-lookup"><span data-stu-id="17f60-192">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="17f60-193">自動再接続試行のタイミングと回数をさらに細かく制御する場合、`withAutomaticReconnect` は、`nextRetryDelayInMilliseconds`という名前の1つのメソッドを持つ `IRetryPolicy` インターフェイスを実装するオブジェクトを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="17f60-193">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="17f60-194">`nextRetryDelayInMilliseconds` は、`RetryContext`型の1つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="17f60-194">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="17f60-195">`RetryContext` には、`previousRetryCount`、`elapsedMilliseconds` と `retryReason` という3つのプロパティがあります。これは、それぞれ `number`、`number`、および `Error` です。</span><span class="sxs-lookup"><span data-stu-id="17f60-195">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="17f60-196">最初の再接続を試行する前に、`previousRetryCount` と `elapsedMilliseconds` の両方がゼロになり、`retryReason` は接続が失われる原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="17f60-196">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="17f60-197">再試行が再試行されるたびに、`previousRetryCount` が1ずつインクリメントされます。これまでの再接続にかかった時間 (ミリ秒単位) が反映されるように `elapsedMilliseconds` が更新され、最後の再接続の試行が失敗した原因となったエラーが `retryReason` ます。</span><span class="sxs-lookup"><span data-stu-id="17f60-197">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="17f60-198">`nextRetryDelayInMilliseconds` は、次の再接続を試行する前に待機するミリ秒数を表す数値を返すか、`HubConnection` の再接続を停止する必要がある場合は `null` する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17f60-198">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="17f60-199">または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="17f60-199">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="17f60-200">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="17f60-200">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="17f60-201">3\.0 より前の SignalR の JavaScript クライアントは、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="17f60-201">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="17f60-202">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17f60-202">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="17f60-203">次のコードは、標準的な手動再接続アプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="17f60-203">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="17f60-204">接続を開始する関数 (この場合は `start` 関数) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="17f60-204">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="17f60-205">接続の `onclose` イベントハンドラーで `start` 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="17f60-205">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="17f60-206">実際の実装では、指数バックオフを使用するか、指定された回数の再試行を行います。</span><span class="sxs-lookup"><span data-stu-id="17f60-206">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17f60-207">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="17f60-207">Additional resources</span></span>

* [<span data-ttu-id="17f60-208">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="17f60-208">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="17f60-209">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="17f60-209">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="17f60-210">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="17f60-210">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="17f60-211">ハブ</span><span class="sxs-lookup"><span data-stu-id="17f60-211">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="17f60-212">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="17f60-212">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="17f60-213">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="17f60-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="17f60-214">クロスオリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="17f60-214">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="17f60-215">[Azure SignalR サービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="17f60-215">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
