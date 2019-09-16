---
title: ASP.NET Core SignalR .NET クライアント
author: bradygaster
description: ASP.NET Core SignalR .NET クライアントに関する情報
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/13/2019
uid: signalr/dotnet-client
ms.openlocfilehash: d2755f652e734bad6447ddeb9a82345dcde25b28
ms.sourcegitcommit: 805f625d16d74e77f02f5f37326e5aceafcb78e3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985492"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="17f2f-103">ASP.NET Core SignalR .NET クライアント</span><span class="sxs-lookup"><span data-stu-id="17f2f-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="17f2f-104">ASP.NET Core SignalR .NET クライアントライブラリを使用すると、.NET アプリから SignalR hub と通信できます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="17f2f-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="17f2f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="17f2f-106">この記事のコードサンプルは、ASP.NET Core SignalR .NET クライアントを使用する WPF アプリです。</span><span class="sxs-lookup"><span data-stu-id="17f2f-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="17f2f-107">SignalR .NET クライアントパッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="17f2f-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="17f2f-108">.NET クライアントが SignalR hub に接続するには、 [SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="17f2f-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="17f2f-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17f2f-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17f2f-110">クライアントライブラリをインストールするには、**パッケージマネージャーコンソール**ウィンドウで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="17f2f-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="17f2f-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="17f2f-112">クライアントライブラリをインストールするには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-112">To install the client library, run the following command in a command shell:</span></span>

```console
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="17f2f-113">ハブへの接続します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-113">Connect to a hub</span></span>

<span data-ttu-id="17f2f-114">接続を確立するには`HubConnectionBuilder` 、を作成し、を呼び出し`Build`ます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="17f2f-115">接続の構築中に、ハブの URL、プロトコル、トランスポートの種類、ログレベル、ヘッダー、およびその他のオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="17f2f-116">任意の`HubConnectionBuilder`メソッドをに`Build`挿入して、必要なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="17f2f-117">との`StartAsync`接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="17f2f-118">失われた接続の処理</span><span class="sxs-lookup"><span data-stu-id="17f2f-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="17f2f-119">自動的に再接続する</span><span class="sxs-lookup"><span data-stu-id="17f2f-119">Automatically reconnect</span></span>

<span data-ttu-id="17f2f-120">は、の`WithAutomaticReconnect`メソッド<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>を使用して自動的に再接続するように構成できます。<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection></span><span class="sxs-lookup"><span data-stu-id="17f2f-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="17f2f-121">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="17f2f-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="17f2f-122">パラメーターを指定し`WithAutomaticReconnect()`ない場合、は、再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="17f2f-123">再接続の試行を開始する`HubConnection`前に、は`HubConnectionState.Reconnecting`状態に遷移し`Reconnecting` 、イベントを発生させます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="17f2f-124">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="17f2f-125">非対話型アプリでは、メッセージのキューまたは削除を開始できます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="17f2f-126">最初の4回の試行でクライアントが正常に再`HubConnection`接続した場合、 `Connected`は状態に戻り`Reconnected` 、イベントを発生させます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="17f2f-127">これにより、接続が再確立されたことをユーザーに通知し、キューに置かれたすべてのメッセージをデキューすることができます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="17f2f-128">接続はサーバーにまったく新しいものであるため、 `ConnectionId` `Reconnected`イベントハンドラーに新しいが提供されます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="17f2f-129">が`Reconnected` [ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成され`HubConnection`ている場合、イベントハンドラーの`connectionId`パラメーターは null になります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="17f2f-130">`WithAutomaticReconnect()`最初の開始`HubConnection`エラーを再試行するようにを構成しません。そのため、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="17f2f-131">最初の4回の試行でクライアントが正常に再接続`HubConnection`されない場合`Disconnected` 、は状態に<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>遷移し、イベントを発生させます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="17f2f-132">これにより、接続を手動で再起動したり、接続が完全に失われたことをユーザーに通知したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="17f2f-133">再接続のタイミングを切断または変更する前に、カスタムの再接続試行`WithAutomaticReconnect`回数を構成するために、では、各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="17f2f-134">前の例では`HubConnection` 、接続が失われた直後に再接続を開始するようにを構成しています。</span><span class="sxs-lookup"><span data-stu-id="17f2f-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="17f2f-135">これは、既定の構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="17f2f-136">最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="17f2f-137">2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="17f2f-138">その後、3回目の再接続の試行が失敗した後に停止することで、カスタム動作が既定の動作から再び逸脱します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="17f2f-139">既定の構成では、もう1回は30秒後に再接続が試行されます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="17f2f-140">タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `WithAutomaticReconnect` `IRetryPolicy`インターフェイスを実装するオブジェクトを受け取ります。このインターフェイスには`NextRetryDelay`、という名前の1つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="17f2f-141">`NextRetryDelay`型`RetryContext`の1つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="17f2f-142">`RetryReason` `ElapsedTime` `long` `PreviousRetryCount` `TimeSpan` には`Exception` 、、、およびの3つのプロパティがあります。`RetryContext`</span><span class="sxs-lookup"><span data-stu-id="17f2f-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="17f2f-143">最初の再接続を試行する`PreviousRetryCount`前`ElapsedTime`に、との`RetryReason`両方がゼロになり、は接続が失われる原因となった例外になります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="17f2f-144">再試行が再試行されるたび`PreviousRetryCount`に、が`ElapsedTime` 1 ずつインクリメントされ、これまでに再接続に費やされた時間が反映さ`RetryReason`れます。また、は、最後の再接続の試行が失敗した原因となった例外になります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="17f2f-145">`NextRetryDelay`次の再接続が試行される前に待機する時間を表す TimeSpan `null`を返す`HubConnection`か、またはの再接続を停止する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.Next() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="17f2f-146">または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="17f2f-147">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="17f2f-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="17f2f-148">3\.0 より前では、SignalR 用の .NET クライアントは自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="17f2f-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="17f2f-149">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17f2f-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="17f2f-150">失われ<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>た接続に応答するには、イベントを使用します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="17f2f-151">たとえば、再接続を自動化することができます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="17f2f-152">イベント`Closed`には`Task`、を返すデリゲートが必要です。これにより、を使用`async void`せずに非同期コードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="17f2f-153">同期的に実行される`Closed`イベントハンドラーでデリゲートシグネチャを満たすには、次を返し`Task.CompletedTask`ます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="17f2f-154">非同期サポートの主な理由は、接続を再起動できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="17f2f-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="17f2f-155">接続の開始は、非同期アクションです。</span><span class="sxs-lookup"><span data-stu-id="17f2f-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="17f2f-156">接続を再起動するハンドラーで、次の例に示すように、サーバーの過負荷を防ぐために、ランダムな遅延を待機することを検討してください。`Closed`</span><span class="sxs-lookup"><span data-stu-id="17f2f-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="17f2f-157">クライアントからのハブ メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="17f2f-157">Call hub methods from client</span></span>

<span data-ttu-id="17f2f-158">`InvokeAsync`ハブでメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="17f2f-159">ハブメソッドの名前と、ハブメソッドで定義されている`InvokeAsync`すべての引数をに渡します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="17f2f-160">SignalR は非同期であるため`async` 、 `await`呼び出しを行うときにとを使用します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="17f2f-161">この`InvokeAsync`メソッドは、 `Task`サーバーメソッドから制御が戻ったときに完了するを返します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="17f2f-162">戻り値 (存在する場合) は、 `Task`の結果として提供されます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="17f2f-163">サーバー上のメソッドによってスローされた例外`Task`が発生すると、エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="17f2f-164">構文`await`を使用して、サーバーメソッドが`try...catch`完了するのを待機し、構文を使用してエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="17f2f-165">メソッド`SendAsync`は、メッセージ`Task`がサーバーに送信されたときに完了するを返します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="17f2f-166">サーバーメソッドが完了するまで待機`Task`しないため、戻り値は提供されません。</span><span class="sxs-lookup"><span data-stu-id="17f2f-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="17f2f-167">メッセージの送信中にクライアントでスローされた例外は`Task`、エラーを生成します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="17f2f-168">および`await`構文`try...catch`を使用して、送信エラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="17f2f-169">*サーバーレスモード*で Azure SignalR サービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="17f2f-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="17f2f-170">詳細については、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17f2f-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="17f2f-171">ハブからのクライアント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="17f2f-171">Call client methods from hub</span></span>

<span data-ttu-id="17f2f-172">ハブがビルド後、接続`connection.On`を開始する前にを使用して呼び出すメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="17f2f-173">の`connection.On`前のコードは、サーバー側のコードが`SendAsync`メソッドを使用して呼び出したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="17f2f-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="17f2f-174">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="17f2f-174">Error handling and logging</span></span>

<span data-ttu-id="17f2f-175">Try-catch ステートメントを使用してエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="17f2f-176">`Exception`オブジェクトを調べて、エラーが発生した後に実行する適切なアクションを決定します。</span><span class="sxs-lookup"><span data-stu-id="17f2f-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="17f2f-177">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="17f2f-177">Additional resources</span></span>

* [<span data-ttu-id="17f2f-178">ハブ</span><span class="sxs-lookup"><span data-stu-id="17f2f-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="17f2f-179">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="17f2f-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="17f2f-180">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="17f2f-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="17f2f-181">Azure SignalR Service サーバーレスドキュメント</span><span class="sxs-lookup"><span data-stu-id="17f2f-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
