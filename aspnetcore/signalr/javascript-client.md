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
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="4b500-103">ASP.NETコアSignalRJavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="4b500-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="4b500-104">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="4b500-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="4b500-105">ASP.NETコアSignalRJavaScript クライアント ライブラリを使用すると、開発者はサーバー側のハブ コードを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4b500-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="4b500-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4b500-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="4b500-107">クライアントSignalRパッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="4b500-107">Install the SignalR client package</span></span>

<span data-ttu-id="4b500-108">SignalR JavaScript クライアント ライブラリは[npm](https://www.npmjs.com/)パッケージとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="4b500-109">次のセクションでは、クライアント ライブラリをインストールするさまざまな方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4b500-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="4b500-110">npm でインストールする</span><span class="sxs-lookup"><span data-stu-id="4b500-110">Install with npm</span></span>

<span data-ttu-id="4b500-111">Visual Studio を使用している場合は、ルート フォルダー内で**パッケージ マネージャー コンソール**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4b500-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="4b500-112">Visual Studio コードの場合は、**統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4b500-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="4b500-113">npm はパッケージの内容を*node_modules\\*フォルダにインストールします。</span><span class="sxs-lookup"><span data-stu-id="4b500-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="4b500-114">*wwwroot\\lib*フォルダの下に*signalr*という名前の新しいフォルダを作成します。</span><span class="sxs-lookup"><span data-stu-id="4b500-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="4b500-115">*signalr.js*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="4b500-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="4b500-116">npm はパッケージの内容を*node_modules\\*フォルダにインストールします。</span><span class="sxs-lookup"><span data-stu-id="4b500-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="4b500-117">*wwwroot\\lib*フォルダの下に*signalr*という名前の新しいフォルダを作成します。</span><span class="sxs-lookup"><span data-stu-id="4b500-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="4b500-118">*signalr.js*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="4b500-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="4b500-119">要素内SignalRの JavaScript`<script>`クライアントを参照します。</span><span class="sxs-lookup"><span data-stu-id="4b500-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="4b500-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4b500-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="4b500-121">コンテンツ配信ネットワーク (CDN) を使用する</span><span class="sxs-lookup"><span data-stu-id="4b500-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="4b500-122">npm の前提条件なしでクライアント ライブラリを使用するには、クライアント ライブラリの CDN ホストコピーを参照します。</span><span class="sxs-lookup"><span data-stu-id="4b500-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="4b500-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4b500-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="4b500-124">クライアント ライブラリは、次の CDN で使用できます。</span><span class="sxs-lookup"><span data-stu-id="4b500-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="4b500-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="4b500-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="4b500-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="4b500-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="4b500-127">アンプク</span><span class="sxs-lookup"><span data-stu-id="4b500-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="4b500-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="4b500-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="4b500-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="4b500-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="4b500-130">アンプク</span><span class="sxs-lookup"><span data-stu-id="4b500-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="4b500-131">LibMan でインストールする</span><span class="sxs-lookup"><span data-stu-id="4b500-131">Install with LibMan</span></span>

<span data-ttu-id="4b500-132">[LibMan](xref:client-side/libman/index)は、CDN ホスト型クライアント ライブラリから特定のクライアント ライブラリ ファイルをインストールするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="4b500-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="4b500-133">たとえば、縮小された JavaScript ファイルのみをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="4b500-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="4b500-134">この方法の詳細については、「[クライアント ライブラリSignalRの追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4b500-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="4b500-135">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="4b500-135">Connect to a hub</span></span>

<span data-ttu-id="4b500-136">次のコードは、接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="4b500-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="4b500-137">ハブの名前は大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="4b500-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="4b500-138">クロスオリジン接続</span><span class="sxs-lookup"><span data-stu-id="4b500-138">Cross-origin connections</span></span>

<span data-ttu-id="4b500-139">通常、ブラウザーは要求されたページと同じドメインから接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="4b500-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="4b500-140">ただし、別のドメインへの接続が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="4b500-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="4b500-141">悪意のあるサイトが別のサイトから機密データを読み取らないようにするために、[既定では、クロスオリジン接続](xref:security/cors)は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="4b500-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="4b500-142">クロスオリジンリクエストを許可するには、クラスでそれを有効にします`Startup`。</span><span class="sxs-lookup"><span data-stu-id="4b500-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="4b500-143">クライアントからハブ メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="4b500-143">Call hub methods from client</span></span>

<span data-ttu-id="4b500-144">JavaScript クライアントは、ハブ接続の[呼び出し](/javascript/api/%40aspnet/signalr/hubconnection#invoke)メソッドを介してハブ上のパブリック メソッドを呼び出[します](/javascript/api/%40aspnet/signalr/hubconnection)。</span><span class="sxs-lookup"><span data-stu-id="4b500-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="4b500-145">この`invoke`メソッドは、次の 2 つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="4b500-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="4b500-146">ハブ メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="4b500-146">The name of the hub method.</span></span> <span data-ttu-id="4b500-147">次の例では、ハブのメソッド名は`SendMessage`です。</span><span class="sxs-lookup"><span data-stu-id="4b500-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="4b500-148">ハブ メソッドで定義されている引数。</span><span class="sxs-lookup"><span data-stu-id="4b500-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="4b500-149">次の例では、引数名は`message`です。</span><span class="sxs-lookup"><span data-stu-id="4b500-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="4b500-150">このコード例では、Internet Explorer 以外のすべての主要ブラウザの現在のバージョンでサポートされている矢印関数構文を使用しています。</span><span class="sxs-lookup"><span data-stu-id="4b500-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="4b500-151">AzureSignalRサービスを*サーバーレス モード*で使用している場合は、クライアントからハブ メソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="4b500-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="4b500-152">詳細については、[SignalRサービスのドキュメントを参照してください](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="4b500-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="4b500-153">この`invoke`メソッドは JavaScript[プロミス](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="4b500-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="4b500-154">サーバー`Promise`上のメソッドが戻るときに、戻り値 (存在する場合) で解決されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="4b500-155">サーバー上のメソッドがエラーをスローした場合、`Promise`はエラー メッセージで拒否されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="4b500-156">メソッド`then`自体`catch`を使用して、`Promise`これらのケース (または`await`構文) を処理します。</span><span class="sxs-lookup"><span data-stu-id="4b500-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="4b500-157">この`send`メソッドは JavaScript`Promise`を返します。</span><span class="sxs-lookup"><span data-stu-id="4b500-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="4b500-158">メッセージ`Promise`がサーバーに送信されると、 が解決されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="4b500-159">メッセージの送信中にエラーが発生した場合`Promise`、 はエラー メッセージで拒否されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="4b500-160">メソッド`then`自体`catch`を使用して、`Promise`これらのケース (または`await`構文) を処理します。</span><span class="sxs-lookup"><span data-stu-id="4b500-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="4b500-161">を`send`使用しても、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="4b500-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="4b500-162">したがって、サーバーからデータやエラーを返す可能性はありません。</span><span class="sxs-lookup"><span data-stu-id="4b500-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="4b500-163">ハブからクライアント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="4b500-163">Call client methods from hub</span></span>

<span data-ttu-id="4b500-164">ハブからメッセージを受信するには、 の[on](/javascript/api/%40aspnet/signalr/hubconnection#on)メソッドを使用して`HubConnection`メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="4b500-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="4b500-165">JavaScript クライアント メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="4b500-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="4b500-166">次の例では、メソッド名は`ReceiveMessage`です。</span><span class="sxs-lookup"><span data-stu-id="4b500-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="4b500-167">ハブがメソッドに渡す引数。</span><span class="sxs-lookup"><span data-stu-id="4b500-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="4b500-168">次の例では、引数値は`message`です。</span><span class="sxs-lookup"><span data-stu-id="4b500-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="4b500-169">上記のコードは、`connection.on`サーバー側コードが[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッドを使用して呼び出したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="4b500-170">で定義されたメソッド名と引数を一致させることによって、どのクライアント`SendAsync``connection.on`メソッドを呼び出すかを決定します。</span><span class="sxs-lookup"><span data-stu-id="4b500-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="4b500-171">ベスト プラクティスとして、after`on`の[start](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection`メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4b500-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="4b500-172">これにより、メッセージを受信する前にハンドラーが登録されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="4b500-173">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="4b500-173">Error handling and logging</span></span>

<span data-ttu-id="4b500-174">メソッドを`catch`メソッドの最後にチェイン`start`して、クライアント側のエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="4b500-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="4b500-175">ブラウザ`console.error`のコンソールにエラーを出力するために使用します。</span><span class="sxs-lookup"><span data-stu-id="4b500-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="4b500-176">接続が確立されたときにログに記録するロガーとイベントの種類を渡すことによって、クライアント側のログ トレースをセットアップします。</span><span class="sxs-lookup"><span data-stu-id="4b500-176">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="4b500-177">メッセージは、指定したログ レベル以上でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="4b500-178">使用可能なログ・レベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4b500-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="4b500-179">`signalR.LogLevel.Error`&ndash;エラー メッセージ。</span><span class="sxs-lookup"><span data-stu-id="4b500-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="4b500-180">メッセージ`Error`のみをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="4b500-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="4b500-181">`signalR.LogLevel.Warning`&ndash;潜在的なエラーに関する警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="4b500-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="4b500-182">ログ`Warning`、`Error`およびメッセージ。</span><span class="sxs-lookup"><span data-stu-id="4b500-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="4b500-183">`signalR.LogLevel.Information`&ndash;エラーのないステータス メッセージ。</span><span class="sxs-lookup"><span data-stu-id="4b500-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="4b500-184">ログ`Information` `Warning`、、`Error`およびメッセージ。</span><span class="sxs-lookup"><span data-stu-id="4b500-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="4b500-185">`signalR.LogLevel.Trace`&ndash;メッセージをトレースします。</span><span class="sxs-lookup"><span data-stu-id="4b500-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="4b500-186">ハブとクライアントの間で転送されるデータを含め、すべてをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="4b500-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="4b500-187">ログ レベルを構成するには[、ハブ接続ビルダー](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)の[ログの構成](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="4b500-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="4b500-188">メッセージはブラウザコンソールに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="4b500-189">クライアントの再接続</span><span class="sxs-lookup"><span data-stu-id="4b500-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="4b500-190">自動的に再接続</span><span class="sxs-lookup"><span data-stu-id="4b500-190">Automatically reconnect</span></span>

<span data-ttu-id="4b500-191">の JavaScriptSignalRクライアントは、[ハブコネクション ビルダー](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)の`withAutomaticReconnect`メソッドを使用して自動的に再接続するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="4b500-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="4b500-192">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="4b500-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="4b500-193">パラメータを指定せずに`withAutomaticReconnect()`、各再接続を試行する前にそれぞれ 0、2、10、および 30 秒待機するようにクライアントを設定し、4 回の試行に失敗した後に停止します。</span><span class="sxs-lookup"><span data-stu-id="4b500-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="4b500-194">再接続の試行を開始する前に`HubConnection`、状態に遷`HubConnectionState.Reconnecting`移`onreconnecting`し、`Disconnected`状態に遷移して、自動再接続を設定せずにコールバックを`onclose`トリガーする代わりに`HubConnection`、コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="4b500-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="4b500-195">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にする機会が得られます。</span><span class="sxs-lookup"><span data-stu-id="4b500-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="4b500-196">クライアントが最初の 4 回の試行で正常に再接続した`HubConnection`場合、 は`Connected`状態に戻り、`onreconnected`コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="4b500-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="4b500-197">これにより、接続が再確立されたことをユーザーに通知できます。</span><span class="sxs-lookup"><span data-stu-id="4b500-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="4b500-198">接続はサーバーにまったく新しく表示されるため、コールバックに`connectionId`新しい情報が`onreconnected`提供されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="4b500-199">ネゴシエーション`onreconnected`をスキップするように`connectionId`設定されている場合、コールバックの`HubConnection`パラメータは未定義[skip negotiation](xref:signalr/configuration#configure-client-options)になります。</span><span class="sxs-lookup"><span data-stu-id="4b500-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="4b500-200">`withAutomaticReconnect()`初期開始エラーを`HubConnection`再試行するように構成されないので、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b500-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="4b500-201">クライアントが最初の 4 回の試行で正常に再接続しない場合は`HubConnection`、`Disconnected`状態に遷移し[、onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="4b500-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="4b500-202">これにより、接続が完全に失われたことをユーザーに通知し、ページを更新することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4b500-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="4b500-203">再接続のタイミングを切断または変更する前に、カスタムの再接続試行回数を設定するために、`withAutomaticReconnect`各再接続の試行を開始するまでの待機時間をミリ秒単位で表す番号の配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="4b500-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="4b500-204">上記の例では、接続`HubConnection`が切断された直後に再接続を開始するように を構成しています。</span><span class="sxs-lookup"><span data-stu-id="4b500-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="4b500-205">これは、デフォルトの構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="4b500-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="4b500-206">最初の再接続が失敗した場合、2 回目の再接続も、既定の構成と同じように 2 秒待つのではなく、すぐに開始されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="4b500-207">2 回目の再接続が失敗すると、3 回目の再接続は 10 秒後に開始され、再びデフォルトの構成と同じになります。</span><span class="sxs-lookup"><span data-stu-id="4b500-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="4b500-208">カスタム動作は、3 回目の再接続の失敗後に停止し、既定の設定のように 30 秒後に再接続を再試行するのではなく、既定の動作と再び異なります。</span><span class="sxs-lookup"><span data-stu-id="4b500-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="4b500-209">自動再接続の試行回数をさらに制御する場合は、`withAutomaticReconnect`という単`IRetryPolicy``nextRetryDelayInMilliseconds`一のメソッドを持つインターフェイスを実装するオブジェクトを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="4b500-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="4b500-210">`nextRetryDelayInMilliseconds`型`RetryContext`を指定して引数を 1 つ取ります。</span><span class="sxs-lookup"><span data-stu-id="4b500-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="4b500-211">には`RetryContext`、`previousRetryCount``elapsedMilliseconds`の 3`retryReason`つのプロパティがあり`number`、それぞれ`number`、`Error`と が 1 つになります。</span><span class="sxs-lookup"><span data-stu-id="4b500-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="4b500-212">最初の再接続が試行される前`previousRetryCount`に`elapsedMilliseconds`、両方ともゼロになり、`retryReason`接続が失われる原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="4b500-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="4b500-213">再試行が失敗するたびに 1`previousRetryCount`ずつ増加し、`elapsedMilliseconds`それまでの再接続にかかった時間をミリ秒単位で反映するように更新され、最後の`retryReason`再接続の失敗の原因となった Error が返されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="4b500-214">`nextRetryDelayInMilliseconds`次の再接続が試行されるまでの待機時間をミリ秒単位で表す数値を返すか`null`、再接続を`HubConnection`停止する必要があるかのいずれかを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b500-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="4b500-215">代わりに、「手動で再接続する」で示すように、クライアントを[手動で再接続](#manually-reconnect)するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="4b500-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="4b500-216">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="4b500-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="4b500-217">3.0 より前のバージョンでは、JavaScriptSignalRクライアントは自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="4b500-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="4b500-218">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b500-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="4b500-219">次のコードは、一般的な手動再接続の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4b500-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="4b500-220">接続を開始する関数 (この`start`場合は関数) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4b500-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="4b500-221">接続の`start``onclose`イベント ハンドラーで関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4b500-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="4b500-222">実際の実装では、指数バックオフを使用するか、指定された回数再試行してから、あきらめます。</span><span class="sxs-lookup"><span data-stu-id="4b500-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b500-223">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="4b500-223">Additional resources</span></span>

* [<span data-ttu-id="4b500-224">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="4b500-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="4b500-225">Java スクリプトのチュートリアル</span><span class="sxs-lookup"><span data-stu-id="4b500-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4b500-226">ウェブパックとタイプスクリプトのチュートリアル</span><span class="sxs-lookup"><span data-stu-id="4b500-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="4b500-227">ハブ</span><span class="sxs-lookup"><span data-stu-id="4b500-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="4b500-228">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="4b500-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4b500-229">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="4b500-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="4b500-230">クロスオリジンリクエスト(CORS)</span><span class="sxs-lookup"><span data-stu-id="4b500-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="4b500-231">[AzureSignalRサービス サーバーレス ドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="4b500-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
