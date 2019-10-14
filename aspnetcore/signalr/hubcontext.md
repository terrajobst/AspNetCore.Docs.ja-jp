---
title: SignalR HubContext
author: bradygaster
description: ハブの外部からのクライアントに通知を送信するための ASP.NET Core SignalR HubContext サービスを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 7ec52d4711fc191dcb83120cf54b1dc28c41f947
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894479"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="0a792-103">ハブの外部からのメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="0a792-103">Send messages from outside a hub</span></span>

<span data-ttu-id="0a792-104">作成者: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="0a792-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="0a792-105">SignalR ハブは、SignalR のサーバーに接続しているクライアントにメッセージを送信するための中核となる抽象化です。</span><span class="sxs-lookup"><span data-stu-id="0a792-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="0a792-106">アプリを使用して、その他の場所からメッセージを送信することも、`IHubContext`サービス。</span><span class="sxs-lookup"><span data-stu-id="0a792-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="0a792-107">この記事は、SignalR にアクセスする方法を説明します`IHubContext`ハブ外からのクライアントに通知を送信します。</span><span class="sxs-lookup"><span data-stu-id="0a792-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="0a792-108">[サンプル コードのダウンロードを表示または](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0a792-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="0a792-109">IHubContext のインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="0a792-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="0a792-110">ASP.NET Core signalr でのインスタンスにアクセスすることができます`IHubContext`依存関係の挿入を使用しています。</span><span class="sxs-lookup"><span data-stu-id="0a792-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="0a792-111">インスタンスを挿入できる`IHubContext`コント ローラー、ミドルウェア、またはその他の DI サービスにします。</span><span class="sxs-lookup"><span data-stu-id="0a792-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="0a792-112">インスタンスを使用して、クライアントにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="0a792-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="0a792-113">一方、ASP.NET 4.x GlobalHost にアクセスできるようにするために使用する SignalR、`IHubContext`します。</span><span class="sxs-lookup"><span data-stu-id="0a792-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="0a792-114">ASP.NET Core は、このグローバル シングルトンの必要性を削除する依存関係挿入フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="0a792-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="0a792-115">IHubContext のコント ローラーのインスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="0a792-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="0a792-116">インスタンスを挿入できる`IHubContext`コンス トラクターに追加して、コント ローラーにします。</span><span class="sxs-lookup"><span data-stu-id="0a792-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="0a792-117">現在のインスタンスへのアクセスで`IHubContext`、ハブ自体で必要がある場合は、ハブ メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="0a792-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="0a792-118">IHubContext のミドルウェア内でインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="0a792-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="0a792-119">アクセス、`IHubContext`ミドルウェア パイプライン内で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="0a792-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> <span data-ttu-id="0a792-120">ハブ メソッドの外部から呼び出されるときに、`Hub`クラスは、呼び出しに関連付けられている呼び出し元はありません。</span><span class="sxs-lookup"><span data-stu-id="0a792-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="0a792-121">そのためへのアクセスはありません、 `ConnectionId`、 `Caller`、および`Others`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="0a792-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="0a792-122">厳密に型指定された HubContext を挿入します。</span><span class="sxs-lookup"><span data-stu-id="0a792-122">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="0a792-123">厳密に型指定された HubContext を挿入するように、ハブが継承`Hub<T>`します。</span><span class="sxs-lookup"><span data-stu-id="0a792-123">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="0a792-124">挿入を使用して、`IHubContext<THub, T>`インターフェイスなく`IHubContext<THub>`します。</span><span class="sxs-lookup"><span data-stu-id="0a792-124">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a><span data-ttu-id="0a792-125">関連資料</span><span class="sxs-lookup"><span data-stu-id="0a792-125">Related resources</span></span>

* [<span data-ttu-id="0a792-126">開始するには</span><span class="sxs-lookup"><span data-stu-id="0a792-126">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0a792-127">ハブ</span><span class="sxs-lookup"><span data-stu-id="0a792-127">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0a792-128">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="0a792-128">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
