---
title: ASP.NET Core SignalR の概要
author: bradygaster
description: ASP.NET Core SignalR ライブラリを通じて、リアルタイムの機能をアプリに簡単に追加する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: e84dd0d086cbfc80a80bc10baa33979da9b5d137
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717235"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a><span data-ttu-id="6fa49-103">ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="6fa49-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-opno-locsignalr"></a><span data-ttu-id="6fa49-104">SignalRとは</span><span class="sxs-lookup"><span data-stu-id="6fa49-104">What is SignalR?</span></span>

<span data-ttu-id="6fa49-105">ASP.NET Core SignalR は、リアルタイムの web 機能をアプリに簡単に追加できるオープンソースライブラリです。</span><span class="sxs-lookup"><span data-stu-id="6fa49-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="6fa49-106">リアルタイム web 機能を使用すると、サーバー側のコードでクライアントにコンテンツを瞬時にプッシュできます。</span><span class="sxs-lookup"><span data-stu-id="6fa49-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="6fa49-107">SignalRの候補として適しています。</span><span class="sxs-lookup"><span data-stu-id="6fa49-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="6fa49-108">サーバーからの高頻度の更新を必要とするアプリ。</span><span class="sxs-lookup"><span data-stu-id="6fa49-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="6fa49-109">例として、ゲーム、ソーシャルネットワーク、投票、オークション、地図、GPS アプリがあります。</span><span class="sxs-lookup"><span data-stu-id="6fa49-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="6fa49-110">ダッシュボードと監視アプリ。</span><span class="sxs-lookup"><span data-stu-id="6fa49-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="6fa49-111">例としては、会社のダッシュボード、インスタント販売の更新、旅行の通知などがあります。</span><span class="sxs-lookup"><span data-stu-id="6fa49-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="6fa49-112">コラボレーションアプリ。</span><span class="sxs-lookup"><span data-stu-id="6fa49-112">Collaborative apps.</span></span> <span data-ttu-id="6fa49-113">共同作業用アプリの例としては、ホワイトボードアプリとチームミーティングソフトウェアがあります。</span><span class="sxs-lookup"><span data-stu-id="6fa49-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="6fa49-114">通知が必要なアプリ。</span><span class="sxs-lookup"><span data-stu-id="6fa49-114">Apps that require notifications.</span></span> <span data-ttu-id="6fa49-115">ソーシャルネットワーク、電子メール、チャット、ゲーム、旅行アラート、およびその他の多くのアプリが通知を使用します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="6fa49-116"> には、サーバー間の[リモートプロシージャ呼び出し (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)を作成するための API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="6fa49-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="6fa49-117">Rpc は、サーバー側 .NET Core コードからクライアントで JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="6fa49-118">ASP.NET Core の SignalR の機能をいくつか次に示します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="6fa49-119">接続管理を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="6fa49-120">接続されているすべてのクライアントに同時にメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="6fa49-121">たとえば、チャットルームなどです。</span><span class="sxs-lookup"><span data-stu-id="6fa49-121">For example, a chat room.</span></span>
* <span data-ttu-id="6fa49-122">特定のクライアントまたはクライアントグループにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="6fa49-123">増加するトラフィックを処理するようにスケーリングします。</span><span class="sxs-lookup"><span data-stu-id="6fa49-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="6fa49-124">ソースは、 [GitHub のSignalR リポジトリ](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)でホストされています。</span><span class="sxs-lookup"><span data-stu-id="6fa49-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="6fa49-125">トランスポート</span><span class="sxs-lookup"><span data-stu-id="6fa49-125">Transports</span></span>

SignalR<span data-ttu-id="6fa49-126"> は、リアルタイム通信 (正常なフォールバックの順序) を処理するための次の手法をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="6fa49-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="6fa49-127">WebSocket</span><span class="sxs-lookup"><span data-stu-id="6fa49-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="6fa49-128">サーバーから送信されたイベント</span><span class="sxs-lookup"><span data-stu-id="6fa49-128">Server-Sent Events</span></span>
* <span data-ttu-id="6fa49-129">長いポーリング</span><span class="sxs-lookup"><span data-stu-id="6fa49-129">Long Polling</span></span>

SignalR<span data-ttu-id="6fa49-130"> は、サーバーとクライアントの機能内にある最適なトランスポート方法を自動的に選択します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="6fa49-131">ハブ</span><span class="sxs-lookup"><span data-stu-id="6fa49-131">Hubs</span></span>

SignalR<span data-ttu-id="6fa49-132"> は、*ハブ*を使用してクライアントとサーバー間の通信を行います。</span><span class="sxs-lookup"><span data-stu-id="6fa49-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="6fa49-133">ハブは、クライアントとサーバーが相互にメソッドを呼び出すことができるようにする、高レベルのパイプラインです。</span><span class="sxs-lookup"><span data-stu-id="6fa49-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="6fa49-134"> は、コンピューターの境界を越えたディスパッチを自動的に処理するので、クライアントはサーバーでメソッドを呼び出すことができ、その逆も可能です。</span><span class="sxs-lookup"><span data-stu-id="6fa49-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="6fa49-135">厳密に型指定されたパラメーターをメソッドに渡すことができます。これにより、モデルバインドが有効になります。</span><span class="sxs-lookup"><span data-stu-id="6fa49-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="6fa49-136"> には、JSON に基づくテキストプロトコルと[Messagepack](https://msgpack.org/)に基づくバイナリプロトコルという2つの組み込みのハブプロトコルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="6fa49-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="6fa49-137">MessagePack では、通常、JSON と比較してより小さなメッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6fa49-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="6fa49-138">以前のブラウザーでは、MessagePack プロトコルのサポートを提供するために、 [Xhr レベル 2](https://caniuse.com/#feat=xhr2)がサポートされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="6fa49-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="6fa49-139">ハブは、クライアント側のメソッドの名前とパラメーターを含むメッセージを送信することによって、クライアント側のコードを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="6fa49-140">メソッドパラメーターとして送信されるオブジェクトは、構成されたプロトコルを使用して逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="6fa49-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="6fa49-141">クライアントは、クライアント側コードのメソッドと名前を一致させようとします。</span><span class="sxs-lookup"><span data-stu-id="6fa49-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="6fa49-142">クライアントが一致を検出すると、メソッドを呼び出し、逆シリアル化されたパラメーターデータに渡します。</span><span class="sxs-lookup"><span data-stu-id="6fa49-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6fa49-143">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6fa49-143">Additional resources</span></span>

* <span data-ttu-id="6fa49-144">[ASP.NET Core の SignalR の概要](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="6fa49-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="6fa49-145">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="6fa49-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="6fa49-146">ハブ</span><span class="sxs-lookup"><span data-stu-id="6fa49-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6fa49-147">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="6fa49-147">JavaScript client</span></span>](xref:signalr/javascript-client)
