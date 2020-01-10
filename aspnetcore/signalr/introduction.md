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
ms.openlocfilehash: 635431abf9263c2dff261aea47e6f8324061763f
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829284"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a><span data-ttu-id="95606-103">ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="95606-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-opno-locsignalr"></a><span data-ttu-id="95606-104">新機能SignalRとは?</span><span class="sxs-lookup"><span data-stu-id="95606-104">What is SignalR?</span></span>

<span data-ttu-id="95606-105">ASP.NET Core SignalR は、リアルタイムの web 機能をアプリに簡単に追加できるオープンソースライブラリです。</span><span class="sxs-lookup"><span data-stu-id="95606-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="95606-106">リアルタイム Web 機能は、サーバー側コードからクライアントにコンテンツを即座にプッシュすることを可能にします。</span><span class="sxs-lookup"><span data-stu-id="95606-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="95606-107">SignalRの候補として適しています。</span><span class="sxs-lookup"><span data-stu-id="95606-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="95606-108">サーバーから頻度の高い更新を必要とするアプリ。</span><span class="sxs-lookup"><span data-stu-id="95606-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="95606-109">例えば、ゲーム、ソーシャル ネットワーク、投票、オークション、マップ、および GPS アプリです。</span><span class="sxs-lookup"><span data-stu-id="95606-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="95606-110">ダッシュ ボードや監視アプリ。</span><span class="sxs-lookup"><span data-stu-id="95606-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="95606-111">会社のダッシュ ボード、即座に更新される販売情報、渡航に関する危険情報、が例に含まれます。</span><span class="sxs-lookup"><span data-stu-id="95606-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="95606-112">コラボレーション アプリ。</span><span class="sxs-lookup"><span data-stu-id="95606-112">Collaborative apps.</span></span> <span data-ttu-id="95606-113">ホワイト ボード アプリやチーム会議ソフトウェアがコラボレーション アプリの例です。</span><span class="sxs-lookup"><span data-stu-id="95606-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="95606-114">通知を必要とするアプリ。</span><span class="sxs-lookup"><span data-stu-id="95606-114">Apps that require notifications.</span></span> <span data-ttu-id="95606-115">ソーシャル ネットワーク、電子メール、チャット、ゲーム、渡航に関する危険情報、およびその他の多くのアプリが通知を使用します。</span><span class="sxs-lookup"><span data-stu-id="95606-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="95606-116"> には、サーバー間の[リモートプロシージャ呼び出し (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)を作成するための API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="95606-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="95606-117">Rpc は、サーバー側 .NET Core コードからクライアントで JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="95606-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="95606-118">ASP.NET Core の SignalR の機能をいくつか次に示します。</span><span class="sxs-lookup"><span data-stu-id="95606-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="95606-119">接続管理を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="95606-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="95606-120">同時に接続されているすべてのクライアントにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="95606-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="95606-121">例えば、チャット ルーム。</span><span class="sxs-lookup"><span data-stu-id="95606-121">For example, a chat room.</span></span>
* <span data-ttu-id="95606-122">特定のクライアントまたはクライアントグループにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="95606-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="95606-123">トラフィックの増加の対応にスケールします。</span><span class="sxs-lookup"><span data-stu-id="95606-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="95606-124">ソースは、 [GitHub のSignalR リポジトリ](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)でホストされています。</span><span class="sxs-lookup"><span data-stu-id="95606-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="95606-125">トランスポート</span><span class="sxs-lookup"><span data-stu-id="95606-125">Transports</span></span>

SignalR<span data-ttu-id="95606-126"> は、リアルタイム通信 (正常なフォールバックの順序) を処理するための次の手法をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="95606-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="95606-127">WebSocket</span><span class="sxs-lookup"><span data-stu-id="95606-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="95606-128">Server-Sent Events</span><span class="sxs-lookup"><span data-stu-id="95606-128">Server-Sent Events</span></span>
* <span data-ttu-id="95606-129">長いポーリング</span><span class="sxs-lookup"><span data-stu-id="95606-129">Long Polling</span></span>

SignalR<span data-ttu-id="95606-130"> は、サーバーとクライアントの機能内にある最適なトランスポート方法を自動的に選択します。</span><span class="sxs-lookup"><span data-stu-id="95606-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="95606-131">ハブ</span><span class="sxs-lookup"><span data-stu-id="95606-131">Hubs</span></span>

SignalR<span data-ttu-id="95606-132"> は、*ハブ*を使用してクライアントとサーバー間の通信を行います。</span><span class="sxs-lookup"><span data-stu-id="95606-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="95606-133">ハブは、相互にメソッドを呼び出すことをクライアントとサーバーに許可する、高度なパイプラインです。</span><span class="sxs-lookup"><span data-stu-id="95606-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="95606-134"> は、コンピューターの境界を越えたディスパッチを自動的に処理するので、クライアントはサーバーでメソッドを呼び出すことができ、その逆も可能です。</span><span class="sxs-lookup"><span data-stu-id="95606-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="95606-135">モデル バインディングを有効にする厳密に型指定されたパラメーターを、メソッドに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="95606-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="95606-136"> には、JSON に基づくテキストプロトコルと[Messagepack](https://msgpack.org/)に基づくバイナリプロトコルという2つの組み込みのハブプロトコルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="95606-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="95606-137">MessagePack は一般に、JSON と比較して小さいメッセージを作成します。</span><span class="sxs-lookup"><span data-stu-id="95606-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="95606-138">MessagePack プロトコル サポートを提供するため、古いブラウザーは[XHR レベル 2](https://caniuse.com/#feat=xhr2)をサポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="95606-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="95606-139">ハブは、クライアント側メソッドのパラメーターと名前を含むメッセージを送信することによって、クライアント側のコードを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="95606-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="95606-140">メソッドのパラメーターとして送信されたオブジェクトは、設定されたプロトコルを使用して逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="95606-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="95606-141">クライアントは、クライアント側コードのメソッド名との一致を試みます。</span><span class="sxs-lookup"><span data-stu-id="95606-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="95606-142">クライアントが一致を検出すると、逆シリアル化されたパラメーター データを渡してメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="95606-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95606-143">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="95606-143">Additional resources</span></span>

* <span data-ttu-id="95606-144">[ASP.NET Core の SignalR の概要](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="95606-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="95606-145">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="95606-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="95606-146">ハブ</span><span class="sxs-lookup"><span data-stu-id="95606-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="95606-147">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="95606-147">JavaScript client</span></span>](xref:signalr/javascript-client)
