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
# <a name="send-messages-from-outside-a-hub"></a>ハブの外部からのメッセージ送信

作成者: [Mikael Mengistu](https://twitter.com/MikaelM_12)

SignalR ハブは、SignalR のサーバーに接続しているクライアントにメッセージを送信するための中核となる抽象化です。 `IHubContext`サービスを使用して、アプリ内の他の場所からメッセージを送信することもできます。 この記事は、ハブの外部からのクライアントに通知を送信するためにSignalR の`IHubContext`にアクセスする方法を説明します。

[サンプルコードの表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>IHubContext インスタンスの取得

ASP.NET Core SignalRでは 依存関係の挿入を介して`IHubContext`インスタンスにアクセスすることができます。 コントローラー、ミドルウェア、またはその他の DI サービスに`IHubContext`インスタンスを挿入できます。クライアントへのメッセージ送信にインスタンスを使用します。

> [!NOTE]
> これは GlobalHost を使用して`IHubContext`へのアクセスを提供した ASP.NET 4.x SignalR とは異なります。 ASP.NET Core には、このグローバルシングルトンの必要性を取り除く依存関係挿入フレームワークがあります。

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>コントローラーへの IHubContext インスタンスの挿入

コンストラクターに`IHubContext`を追加することでコントローラーにそのインスタンスを挿入できます。

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

これで、`IHubContext`インスタンスにアクセスすると、ハブ自体の中のようにハブメソッドを呼び出すことができます。

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>ミドルウェア内での IHubContext インスタンスの取得

ミドルウェアパイプライン内で`IHubContext`にアクセスするには次のようにします。

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> `Hub`クラスの外部からハブメソッドが呼び出されるときは、呼び出しに関連付けられている呼び出し元はありません。 そのため `ConnectionId`、`Caller`、および`Others`プロパティへアクセスすることはできません。

### <a name="inject-a-strongly-typed-hubcontext"></a>厳密に型指定された HubContext の挿入

厳密に型指定された HubContext を挿入するには、ハブが`Hub<T>`を継承していることを確認します。`IHubContext<THub>`ではなく`IHubContext<THub, T>`インターフェースを使用して挿入します。

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

## <a name="related-resources"></a>関連資料

* [開始するには](xref:tutorials/signalr)
* [ハブ](xref:signalr/hubs)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
