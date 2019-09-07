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
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="ff15d-103">SignalR のハブを使用して ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff15d-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="ff15d-104">[Rachel appel](https://twitter.com/rachelappel)および[加山 Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="ff15d-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="ff15d-105">[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ )する[(ダウンロード方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ff15d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="ff15d-106">SignalR hub とは</span><span class="sxs-lookup"><span data-stu-id="ff15d-106">What is a SignalR hub</span></span>

<span data-ttu-id="ff15d-107">SignalR Hub API を使用すると、サーバーから接続されたクライアントでメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="ff15d-108">サーバーコードでは、クライアントによって呼び出されるメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="ff15d-109">クライアントコードでは、サーバーから呼び出されるメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="ff15d-110">SignalR は、クライアントとサーバー間のリアルタイム通信とサーバー間の通信を可能にする、バックグラウンドの背後にあるすべての処理を行います。</span><span class="sxs-lookup"><span data-stu-id="ff15d-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="ff15d-111">SignalR hub を構成する</span><span class="sxs-lookup"><span data-stu-id="ff15d-111">Configure SignalR hubs</span></span>

<span data-ttu-id="ff15d-112">SignalR ミドルウェアには、を呼び出す`services.AddSignalR`ことによって構成されるいくつかのサービスが必要です。</span><span class="sxs-lookup"><span data-stu-id="ff15d-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff15d-113">ASP.NET Core アプリに SignalR 機能を追加する場合、 `endpoint.MapHub` `Startup.Configure`メソッドの`app.UseEndpoints`コールバックでを呼び出して、SignalR のルートを設定します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ff15d-114">SignalR 機能を ASP.NET Core アプリに追加する場合は、 `app.UseSignalR` `Startup.Configure`メソッドでを呼び出して、SignalR のルートを設定します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="ff15d-115">ハブの作成と使用</span><span class="sxs-lookup"><span data-stu-id="ff15d-115">Create and use hubs</span></span>

<span data-ttu-id="ff15d-116">から`Hub`継承するクラスを宣言してハブを作成し、パブリックメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="ff15d-117">クライアントは、と`public`して定義されているメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="ff15d-118">任意C#のメソッドの場合と同様に、戻り値の型とパラメーター (複合型や配列を含む) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="ff15d-119">SignalR は、パラメーターと戻り値の複合オブジェクトおよび配列のシリアル化と逆シリアル化を処理します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-119">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="ff15d-120">ハブは一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="ff15d-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="ff15d-121">ハブクラスのプロパティに状態を格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff15d-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="ff15d-122">すべてのハブメソッド呼び出しは、新しいハブインスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="ff15d-123">ハブ`await`をキープアライブに依存する非同期メソッドを呼び出すときに使用します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="ff15d-124">たとえば、を指定せず`Clients.All.SendAsync(...)` `await`に呼び出され、ハブメソッドが終了する前に`SendAsync`完了した場合、などのメソッドは失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="ff15d-125">コンテキストオブジェクト</span><span class="sxs-lookup"><span data-stu-id="ff15d-125">The Context object</span></span>

<span data-ttu-id="ff15d-126">クラス`Hub` に`Context`は、接続に関する情報を含む次のプロパティを含むプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="ff15d-127">プロパティ</span><span class="sxs-lookup"><span data-stu-id="ff15d-127">Property</span></span> | <span data-ttu-id="ff15d-128">説明</span><span class="sxs-lookup"><span data-stu-id="ff15d-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="ff15d-129">SignalR によって割り当てられる、接続の一意の ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="ff15d-130">接続ごとに1つの接続 ID があります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="ff15d-131">[ユーザー識別子](xref:signalr/groups)を取得します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="ff15d-132">既定では、SignalR は`ClaimTypes.NameIdentifier` 、接続`ClaimsPrincipal`に関連付けられているのをユーザー識別子として使用します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="ff15d-133">現在のユーザーに関連付けられているを取得します。`ClaimsPrincipal`</span><span class="sxs-lookup"><span data-stu-id="ff15d-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="ff15d-134">この接続のスコープ内でデータを共有するために使用できるキー/値のコレクションを取得します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="ff15d-135">このコレクションにデータを格納することができ、さまざまなハブメソッド呼び出し間の接続が維持されます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="ff15d-136">接続で使用できる機能のコレクションを取得します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="ff15d-137">現時点では、ほとんどのシナリオでこのコレクションは必要ないため、詳細には記載されていません。</span><span class="sxs-lookup"><span data-stu-id="ff15d-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="ff15d-138">接続が`CancellationToken`中止されたときに通知するを取得します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="ff15d-139">`Hub.Context`には、次のメソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="ff15d-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="ff15d-140">メソッド</span><span class="sxs-lookup"><span data-stu-id="ff15d-140">Method</span></span> | <span data-ttu-id="ff15d-141">説明</span><span class="sxs-lookup"><span data-stu-id="ff15d-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="ff15d-142">接続のを返します。接続`null`が HTTP 要求に関連付けられていない場合はを返します。 `HttpContext`</span><span class="sxs-lookup"><span data-stu-id="ff15d-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="ff15d-143">HTTP 接続の場合は、このメソッドを使用して、HTTP ヘッダーやクエリ文字列などの情報を取得できます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="ff15d-144">接続を中止します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="ff15d-145">クライアントオブジェクト</span><span class="sxs-lookup"><span data-stu-id="ff15d-145">The Clients object</span></span>

<span data-ttu-id="ff15d-146">クラス`Hub` に`Clients`は、サーバーとクライアント間の通信に関する次のプロパティを含むプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="ff15d-147">プロパティ</span><span class="sxs-lookup"><span data-stu-id="ff15d-147">Property</span></span> | <span data-ttu-id="ff15d-148">説明</span><span class="sxs-lookup"><span data-stu-id="ff15d-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="ff15d-149">接続されているすべてのクライアントでメソッドを呼び出します</span><span class="sxs-lookup"><span data-stu-id="ff15d-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="ff15d-150">ハブメソッドを呼び出したクライアントでメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="ff15d-151">メソッドを呼び出したクライアントを除く、接続されているすべてのクライアントでメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="ff15d-152">`Hub.Clients`には、次のメソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="ff15d-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="ff15d-153">メソッド</span><span class="sxs-lookup"><span data-stu-id="ff15d-153">Method</span></span> | <span data-ttu-id="ff15d-154">説明</span><span class="sxs-lookup"><span data-stu-id="ff15d-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="ff15d-155">指定された接続を除く、接続されているすべてのクライアントでメソッドを呼び出します</span><span class="sxs-lookup"><span data-stu-id="ff15d-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="ff15d-156">特定の接続されたクライアントでメソッドを呼び出します</span><span class="sxs-lookup"><span data-stu-id="ff15d-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="ff15d-157">特定の接続されたクライアントでメソッドを呼び出します</span><span class="sxs-lookup"><span data-stu-id="ff15d-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="ff15d-158">指定されたグループ内のすべての接続でメソッドを呼び出します</span><span class="sxs-lookup"><span data-stu-id="ff15d-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="ff15d-159">指定された接続を除く、指定されたグループ内のすべての接続でメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="ff15d-160">複数の接続グループに対してメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="ff15d-161">ハブメソッドを呼び出したクライアントを除く、接続のグループでメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="ff15d-162">特定のユーザーに関連付けられているすべての接続でメソッドを呼び出します</span><span class="sxs-lookup"><span data-stu-id="ff15d-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="ff15d-163">指定されたユーザーに関連付けられているすべての接続でメソッドを呼び出します</span><span class="sxs-lookup"><span data-stu-id="ff15d-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="ff15d-164">前の表の各プロパティまたはメソッドは、 `SendAsync`メソッドを使用してオブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="ff15d-165">`SendAsync`メソッドを使用すると、クライアントメソッドの名前とパラメーターを指定して呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="ff15d-166">クライアントへのメッセージの送信</span><span class="sxs-lookup"><span data-stu-id="ff15d-166">Send messages to clients</span></span>

<span data-ttu-id="ff15d-167">特定のクライアントに対する呼び出しを行うには、 `Clients`オブジェクトのプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="ff15d-168">次の例には、3つのハブメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="ff15d-169">`SendMessage`を使用`Clients.All`して、接続されているすべてのクライアントにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="ff15d-170">`SendMessageToCaller`を使用して`Clients.Caller`、メッセージを呼び出し元に返信します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="ff15d-171">`SendMessageToGroups``SignalR Users`グループ内のすべてのクライアントにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="ff15d-172">厳密に型指定されたハブ</span><span class="sxs-lookup"><span data-stu-id="ff15d-172">Strongly typed hubs</span></span>

<span data-ttu-id="ff15d-173">を使用`SendAsync`する場合の欠点は、呼び出されるクライアントメソッドを指定するマジック文字列に依存することです。</span><span class="sxs-lookup"><span data-stu-id="ff15d-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="ff15d-174">これにより、メソッド名のスペルが間違っている場合、またはクライアントに存在しない場合、コードは実行時エラーになります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="ff15d-175">を使用する代わり`SendAsync`に<xref:Microsoft.AspNetCore.SignalR.Hub%601>、 `Hub`を使用してを厳密に型にする方法もあります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="ff15d-176">次の例では`ChatHub` 、クライアントメソッドがという`IChatClient`インターフェイスに抽出されています。</span><span class="sxs-lookup"><span data-stu-id="ff15d-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="ff15d-177">このインターフェイスは、前`ChatHub`の例をリファクターするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="ff15d-178">を`Hub<IChatClient>`使用すると、クライアントメソッドのコンパイル時チェックが有効になります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="ff15d-179">これにより、は、インターフェイスで定義さ`Hub<T>`れたメソッドへのアクセスのみを提供できるため、マジック文字列を使用した場合に発生する問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="ff15d-180">厳密に型指定`Hub<T>`されたを使用`SendAsync`すると、を使用することができなくなります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="ff15d-181">インターフェイスで定義されているメソッドは、引き続き非同期として定義できます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="ff15d-182">実際、これらの各メソッドはを`Task`返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="ff15d-183">これはインターフェイスであるため、 `async`キーワードを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff15d-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="ff15d-184">例:</span><span class="sxs-lookup"><span data-stu-id="ff15d-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="ff15d-185">この`Async`サフィックスは、メソッド名から削除されません。</span><span class="sxs-lookup"><span data-stu-id="ff15d-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="ff15d-186">クライアントメソッドがで`.on('MyMethodAsync')`定義されていない場合は、を名前として使用`MyMethodAsync`しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff15d-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="ff15d-187">ハブメソッドの名前を変更する</span><span class="sxs-lookup"><span data-stu-id="ff15d-187">Change the name of a hub method</span></span>

<span data-ttu-id="ff15d-188">既定では、サーバーハブのメソッド名は .NET メソッドの名前です。</span><span class="sxs-lookup"><span data-stu-id="ff15d-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="ff15d-189">ただし、 [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute)属性を使用してこの既定値を変更し、メソッドの名前を手動で指定することができます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="ff15d-190">クライアントは、メソッドを呼び出すときに、.NET メソッド名の代わりにこの名前を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="ff15d-191">接続のイベントを処理する</span><span class="sxs-lookup"><span data-stu-id="ff15d-191">Handle events for a connection</span></span>

<span data-ttu-id="ff15d-192">SignalR hub API は、接続`OnConnectedAsync`の`OnDisconnectedAsync`管理と追跡のためのおよび仮想メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="ff15d-193">`OnConnectedAsync`仮想メソッドを上書きして、クライアントがハブに接続したときにアクションを実行します (グループへの追加など)。</span><span class="sxs-lookup"><span data-stu-id="ff15d-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="ff15d-194">クライアントの`OnDisconnectedAsync`接続が切断されたときにアクションを実行するには、仮想メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ff15d-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="ff15d-195">クライアントが意図的に (たとえば、を`connection.stop()`呼び出すことによって`exception` ) 切断さ`null`れた場合、パラメーターはになります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="ff15d-196">ただし、エラー (ネットワークエラーなど) `exception`が原因でクライアントが切断された場合、パラメーターにはエラーを説明する例外が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="ff15d-197">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="ff15d-197">Handle errors</span></span>

<span data-ttu-id="ff15d-198">ハブメソッドでスローされた例外は、メソッドを呼び出したクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="ff15d-199">Javascript クライアントでは、メソッド`invoke`は javascript の[約束](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)を返します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="ff15d-200">クライアントは、を使用して`catch`promise にアタッチされたハンドラーでエラーを受信すると、それを呼び出し、JavaScript `Error`オブジェクトとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="ff15d-201">ハブが例外をスローした場合、接続は閉じられません。</span><span class="sxs-lookup"><span data-stu-id="ff15d-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="ff15d-202">既定では、SignalR は、一般的なエラーメッセージをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="ff15d-203">例えば:</span><span class="sxs-lookup"><span data-stu-id="ff15d-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="ff15d-204">予期しない例外には、データベース接続が失敗したときにトリガーされる例外のデータベースサーバーの名前など、重要な情報が含まれていることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="ff15d-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="ff15d-205">SignalR では、これらの詳細なエラーメッセージは既定でセキュリティ対策として公開されません。</span><span class="sxs-lookup"><span data-stu-id="ff15d-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="ff15d-206">例外の詳細が抑制される理由の詳細については、「[セキュリティに関する考慮事項](xref:signalr/security#exceptions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff15d-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="ff15d-207">クライアントに伝達する必要のある*例外的な条件*がある場合は、 `HubException`クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff15d-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="ff15d-208">ハブメソッドからを`HubException`スローした場合、SignalR**は**メッセージ全体を変更せずにクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="ff15d-209">SignalR は、例外`Message`のプロパティをクライアントにのみ送信します。</span><span class="sxs-lookup"><span data-stu-id="ff15d-209">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="ff15d-210">例外のスタックトレースおよびその他のプロパティは、クライアントでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="ff15d-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="ff15d-211">関連資料</span><span class="sxs-lookup"><span data-stu-id="ff15d-211">Related resources</span></span>

* [<span data-ttu-id="ff15d-212">ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="ff15d-212">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="ff15d-213">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="ff15d-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="ff15d-214">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="ff15d-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
