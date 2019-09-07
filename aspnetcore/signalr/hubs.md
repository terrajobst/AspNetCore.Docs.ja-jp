---
title: ASP.NET Core SignalR でのハブの使用
author: bradygaster
description: ASP.NET Core SignalR でハブを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: 4922d6d780b18727d3ac181b94dbf75458d74fe6
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746513"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>SignalR のハブを使用して ASP.NET Core

[Rachel appel](https://twitter.com/rachelappel)および[加山 Griffin](https://twitter.com/1kevgriff)

[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ )する[(ダウンロード方法)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>SignalR hub とは

SignalR Hub API を使用すると、サーバーから接続されたクライアントでメソッドを呼び出すことができます。 サーバーコードでは、クライアントによって呼び出されるメソッドを定義します。 クライアントコードでは、サーバーから呼び出されるメソッドを定義します。 SignalR は、クライアントとサーバー間のリアルタイム通信とサーバー間の通信を可能にする、バックグラウンドの背後にあるすべての処理を行います。

## <a name="configure-signalr-hubs"></a>SignalR hub を構成する

SignalR ミドルウェアには、を呼び出す`services.AddSignalR`ことによって構成されるいくつかのサービスが必要です。

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core アプリに SignalR 機能を追加する場合、 `endpoint.MapHub` `Startup.Configure`メソッドの`app.UseEndpoints`コールバックでを呼び出して、SignalR のルートを設定します。

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

SignalR 機能を ASP.NET Core アプリに追加する場合は、 `app.UseSignalR` `Startup.Configure`メソッドでを呼び出して、SignalR のルートを設定します。

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>ハブの作成と使用

から`Hub`継承するクラスを宣言してハブを作成し、パブリックメソッドを追加します。 クライアントは、と`public`して定義されているメソッドを呼び出すことができます。

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

任意C#のメソッドの場合と同様に、戻り値の型とパラメーター (複合型や配列を含む) を指定できます。 SignalR は、パラメーターと戻り値の複合オブジェクトおよび配列のシリアル化と逆シリアル化を処理します。

> [!NOTE]
> ハブは一時的なものです。
>
> * ハブクラスのプロパティに状態を格納しないでください。 すべてのハブメソッド呼び出しは、新しいハブインスタンスで実行されます。
> * ハブ`await`をキープアライブに依存する非同期メソッドを呼び出すときに使用します。 たとえば、を指定せず`Clients.All.SendAsync(...)` `await`に呼び出され、ハブメソッドが終了する前に`SendAsync`完了した場合、などのメソッドは失敗する可能性があります。

## <a name="the-context-object"></a>コンテキストオブジェクト

クラス`Hub` に`Context`は、接続に関する情報を含む次のプロパティを含むプロパティがあります。

| プロパティ | 説明 |
| ------ | ----------- |
| `ConnectionId` | SignalR によって割り当てられる、接続の一意の ID を取得します。 接続ごとに1つの接続 ID があります。|
| `UserIdentifier` | [ユーザー識別子](xref:signalr/groups)を取得します。 既定では、SignalR は`ClaimTypes.NameIdentifier` 、接続`ClaimsPrincipal`に関連付けられているのをユーザー識別子として使用します。 |
| `User` | 現在のユーザーに関連付けられているを取得します。`ClaimsPrincipal` |
| `Items` | この接続のスコープ内でデータを共有するために使用できるキー/値のコレクションを取得します。 このコレクションにデータを格納することができ、さまざまなハブメソッド呼び出し間の接続が維持されます。 |
| `Features` | 接続で使用できる機能のコレクションを取得します。 現時点では、ほとんどのシナリオでこのコレクションは必要ないため、詳細には記載されていません。 |
| `ConnectionAborted` | 接続が`CancellationToken`中止されたときに通知するを取得します。 |

`Hub.Context`には、次のメソッドも含まれています。

| メソッド | 説明 |
| ------ | ----------- |
| `GetHttpContext` | 接続のを返します。接続`null`が HTTP 要求に関連付けられていない場合はを返します。 `HttpContext` HTTP 接続の場合は、このメソッドを使用して、HTTP ヘッダーやクエリ文字列などの情報を取得できます。 |
| `Abort` | 接続を中止します。 |

## <a name="the-clients-object"></a>クライアントオブジェクト

クラス`Hub` に`Clients`は、サーバーとクライアント間の通信に関する次のプロパティを含むプロパティがあります。

| プロパティ | 説明 |
| ------ | ----------- |
| `All` | 接続されているすべてのクライアントでメソッドを呼び出します |
| `Caller` | ハブメソッドを呼び出したクライアントでメソッドを呼び出します。 |
| `Others` | メソッドを呼び出したクライアントを除く、接続されているすべてのクライアントでメソッドを呼び出します。 |

`Hub.Clients`には、次のメソッドも含まれています。

| メソッド | 説明 |
| ------ | ----------- |
| `AllExcept` | 指定された接続を除く、接続されているすべてのクライアントでメソッドを呼び出します |
| `Client` | 特定の接続されたクライアントでメソッドを呼び出します |
| `Clients` | 特定の接続されたクライアントでメソッドを呼び出します |
| `Group` | 指定されたグループ内のすべての接続でメソッドを呼び出します  |
| `GroupExcept` | 指定された接続を除く、指定されたグループ内のすべての接続でメソッドを呼び出します。 |
| `Groups` | 複数の接続グループに対してメソッドを呼び出します。  |
| `OthersInGroup` | ハブメソッドを呼び出したクライアントを除く、接続のグループでメソッドを呼び出します。  |
| `User` | 特定のユーザーに関連付けられているすべての接続でメソッドを呼び出します |
| `Users` | 指定されたユーザーに関連付けられているすべての接続でメソッドを呼び出します |

前の表の各プロパティまたはメソッドは、 `SendAsync`メソッドを使用してオブジェクトを返します。 `SendAsync`メソッドを使用すると、クライアントメソッドの名前とパラメーターを指定して呼び出すことができます。

## <a name="send-messages-to-clients"></a>クライアントへのメッセージの送信

特定のクライアントに対する呼び出しを行うには、 `Clients`オブジェクトのプロパティを使用します。 次の例には、3つのハブメソッドがあります。

* `SendMessage`を使用`Clients.All`して、接続されているすべてのクライアントにメッセージを送信します。
* `SendMessageToCaller`を使用して`Clients.Caller`、メッセージを呼び出し元に返信します。
* `SendMessageToGroups``SignalR Users`グループ内のすべてのクライアントにメッセージを送信します。

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>厳密に型指定されたハブ

を使用`SendAsync`する場合の欠点は、呼び出されるクライアントメソッドを指定するマジック文字列に依存することです。 これにより、メソッド名のスペルが間違っている場合、またはクライアントに存在しない場合、コードは実行時エラーになります。

を使用する代わり`SendAsync`に<xref:Microsoft.AspNetCore.SignalR.Hub%601>、 `Hub`を使用してを厳密に型にする方法もあります。 次の例では`ChatHub` 、クライアントメソッドがという`IChatClient`インターフェイスに抽出されています。

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

このインターフェイスは、前`ChatHub`の例をリファクターするために使用できます。

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

を`Hub<IChatClient>`使用すると、クライアントメソッドのコンパイル時チェックが有効になります。 これにより、は、インターフェイスで定義さ`Hub<T>`れたメソッドへのアクセスのみを提供できるため、マジック文字列を使用した場合に発生する問題を回避できます。

厳密に型指定`Hub<T>`されたを使用`SendAsync`すると、を使用することができなくなります。 インターフェイスで定義されているメソッドは、引き続き非同期として定義できます。 実際、これらの各メソッドはを`Task`返す必要があります。 これはインターフェイスであるため、 `async`キーワードを使用しないでください。 例:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> この`Async`サフィックスは、メソッド名から削除されません。 クライアントメソッドがで`.on('MyMethodAsync')`定義されていない場合は、を名前として使用`MyMethodAsync`しないでください。

## <a name="change-the-name-of-a-hub-method"></a>ハブメソッドの名前を変更する

既定では、サーバーハブのメソッド名は .NET メソッドの名前です。 ただし、 [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute)属性を使用してこの既定値を変更し、メソッドの名前を手動で指定することができます。 クライアントは、メソッドを呼び出すときに、.NET メソッド名の代わりにこの名前を使用する必要があります。

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>接続のイベントを処理する

SignalR hub API は、接続`OnConnectedAsync`の`OnDisconnectedAsync`管理と追跡のためのおよび仮想メソッドを提供します。 `OnConnectedAsync`仮想メソッドを上書きして、クライアントがハブに接続したときにアクションを実行します (グループへの追加など)。

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

クライアントの`OnDisconnectedAsync`接続が切断されたときにアクションを実行するには、仮想メソッドをオーバーライドします。 クライアントが意図的に (たとえば、を`connection.stop()`呼び出すことによって`exception` ) 切断さ`null`れた場合、パラメーターはになります。 ただし、エラー (ネットワークエラーなど) `exception`が原因でクライアントが切断された場合、パラメーターにはエラーを説明する例外が含まれます。

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>エラーの処理

ハブメソッドでスローされた例外は、メソッドを呼び出したクライアントに送信されます。 Javascript クライアントでは、メソッド`invoke`は javascript の[約束](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)を返します。 クライアントは、を使用して`catch`promise にアタッチされたハンドラーでエラーを受信すると、それを呼び出し、JavaScript `Error`オブジェクトとして渡されます。

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

ハブが例外をスローした場合、接続は閉じられません。 既定では、SignalR は、一般的なエラーメッセージをクライアントに返します。 例えば:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

予期しない例外には、データベース接続が失敗したときにトリガーされる例外のデータベースサーバーの名前など、重要な情報が含まれていることがよくあります。 SignalR では、これらの詳細なエラーメッセージは既定でセキュリティ対策として公開されません。 例外の詳細が抑制される理由の詳細については、「[セキュリティに関する考慮事項](xref:signalr/security#exceptions)」を参照してください。

クライアントに伝達する必要のある*例外的な条件*がある場合は、 `HubException`クラスを使用できます。 ハブメソッドからを`HubException`スローした場合、SignalR**は**メッセージ全体を変更せずにクライアントに送信します。

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR は、例外`Message`のプロパティをクライアントにのみ送信します。 例外のスタックトレースおよびその他のプロパティは、クライアントでは使用できません。

## <a name="related-resources"></a>関連資料

* [ASP.NET Core SignalR の概要](xref:signalr/introduction)
* [JavaScript クライアント](xref:signalr/javascript-client)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
