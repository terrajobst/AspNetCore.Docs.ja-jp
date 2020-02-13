---
title: ASP.NET Core SignalR 運用環境のホスティングとスケーリング
author: bradygaster
description: ASP.NET Core SignalRを使用するアプリのパフォーマンスとスケーリングに関する問題を回避する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- SignalR
uid: signalr/scale
ms.openlocfilehash: bb32bb8617f8a3e4170eeb7e38696ee2bbcafe03
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172551"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="4a26f-103">ASP.NET Core SignalR のホストとスケーリング</span><span class="sxs-lookup"><span data-stu-id="4a26f-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="4a26f-104">[Andrew](https://twitter.com/anurse)、 [Brady](https://twitter.com/bradygaster)、および[Tom Dykstra](https://github.com/tdykstra)によって、</span><span class="sxs-lookup"><span data-stu-id="4a26f-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="4a26f-105">この記事では、ASP.NET Core SignalR を使用する高トラフィックアプリのホストとスケーリングに関する考慮事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="4a26f-106">固定セッション</span><span class="sxs-lookup"><span data-stu-id="4a26f-106">Sticky Sessions</span></span>

<span data-ttu-id="4a26f-107">SignalR を使用するには、特定の接続に対するすべての HTTP 要求を同じサーバープロセスで処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="4a26f-108">SignalR がサーバーファーム (複数のサーバー) で実行されている場合は、"固定セッション" を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="4a26f-109">"固定セッション" は、一部のロードバランサーによってセッションアフィニティとも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="4a26f-110">Azure App Service は、[アプリケーション要求ルーティング](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview)処理 (ARR) を使用して要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4a26f-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="4a26f-111">Azure App Service で "ARR Affinity" 設定を有効にすると、"固定セッション" が有効になります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="4a26f-112">固定セッションが不要な状況は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4a26f-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="4a26f-113">単一のサーバーでホストする場合、1つのプロセスで実行します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="4a26f-114">Azure SignalR サービスを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="4a26f-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="4a26f-115">すべてのクライアントが Websocket**のみ**を使用するように構成され、 [skipnegotiation 設定](xref:signalr/configuration#configure-additional-options)がクライアント構成で有効に**なっている**場合。</span><span class="sxs-lookup"><span data-stu-id="4a26f-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="4a26f-116">その他のすべての状況 (Redis バックプレーンを使用する場合を含む) では、サーバー環境を固定セッション用に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="4a26f-117">SignalR の Azure App Service の構成に関するガイダンスについては、「<xref:signalr/publish-to-azure-web-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a26f-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="4a26f-118">TCP 接続リソース</span><span class="sxs-lookup"><span data-stu-id="4a26f-118">TCP connection resources</span></span>

<span data-ttu-id="4a26f-119">Web サーバーがサポートできる同時 TCP 接続の数は制限されています。</span><span class="sxs-lookup"><span data-stu-id="4a26f-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="4a26f-120">標準 HTTP クライアントは、*一時的*な接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="4a26f-121">これらの接続は、クライアントがアイドル状態になったときに終了し、後で再度開くことができます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="4a26f-122">一方、SignalR 接続は*永続的*です。</span><span class="sxs-lookup"><span data-stu-id="4a26f-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="4a26f-123">クライアントがアイドル状態になっても、SignalR 接続は開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="4a26f-124">多数のクライアントにサービスを提供する高トラフィックアプリでは、これらの永続的な接続により、サーバーが最大接続数に達する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="4a26f-125">また、固定接続では、各接続を追跡するために、追加のメモリがいくつか消費されます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="4a26f-126">SignalR によって接続関連のリソースが多用されると、同じサーバー上でホストされている他の web アプリに影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="4a26f-127">SignalR が開いたときに、使用可能な最後の TCP 接続が保持されている場合、同じサーバー上の他の web アプリにも使用できる接続がありません。</span><span class="sxs-lookup"><span data-stu-id="4a26f-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="4a26f-128">サーバーの接続が不足している場合は、ランダムソケットエラーと接続リセットエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="4a26f-129">例 :</span><span class="sxs-lookup"><span data-stu-id="4a26f-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="4a26f-130">SignalR リソースの使用量が他の web アプリでエラーを発生させないようにするには、他の web アプリとは異なるサーバーで SignalR を実行します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="4a26f-131">SignalR リソース使用量が SignalR アプリでエラーを発生させないようにするには、スケールアウトして、サーバーが処理する接続の数を制限します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="4a26f-132">スケール アウト</span><span class="sxs-lookup"><span data-stu-id="4a26f-132">Scale out</span></span>

<span data-ttu-id="4a26f-133">SignalR を使用するアプリでは、そのすべての接続を追跡する必要があります。これにより、サーバーファームに関する問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="4a26f-134">サーバーを追加すると、他のサーバーが認識していない新しい接続が取得されます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="4a26f-135">たとえば、次の図の各サーバーの SignalR は、他のサーバー上の接続を認識していません。</span><span class="sxs-lookup"><span data-stu-id="4a26f-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="4a26f-136">いずれかのサーバーで SignalR がすべてのクライアントにメッセージを送信しようとすると、メッセージはそのサーバーに接続されているクライアントのみに送られます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![バックプレーンを使用しない SignalR のスケーリング](scale/_static/scale-no-backplane.png)

<span data-ttu-id="4a26f-138">この問題を解決するためのオプションは、 [Azure SignalR サービス](#azure-signalr-service)と[Redis バックプレーン](#redis-backplane)です。</span><span class="sxs-lookup"><span data-stu-id="4a26f-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="4a26f-139">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="4a26f-139">Azure SignalR Service</span></span>

<span data-ttu-id="4a26f-140">Azure SignalR サービスは、バックプレーンではなくプロキシです。</span><span class="sxs-lookup"><span data-stu-id="4a26f-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="4a26f-141">クライアントがサーバーへの接続を開始するたびに、クライアントはサービスに接続するためにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="4a26f-142">このプロセスを次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-142">That process is illustrated in the following diagram:</span></span>

![Azure SignalR サービスへの接続を確立する](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="4a26f-144">その結果、次の図に示すように、サービスはすべてのクライアント接続を管理しますが、各サーバーにはサービスへの接続数をごくわずかにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![サービスに接続されているクライアント、サービスに接続されているサーバー](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="4a26f-146">このスケールアウトのアプローチには、Redis バックプレーンよりもいくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="4a26f-147">クライアント[アフィニティ](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)とも呼ばれる固定セッションは、接続時にクライアントが Azure SignalR サービスに直ちにリダイレクトされるため、必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="4a26f-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="4a26f-148">SignalR アプリは、送信されたメッセージの数に基づいてスケールアウトできます。一方、Azure SignalR サービスは、任意の数の接続を処理するように自動的にスケーリングします。</span><span class="sxs-lookup"><span data-stu-id="4a26f-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service automatically scales to handle any number of connections.</span></span> <span data-ttu-id="4a26f-149">たとえば、クライアントが何千も存在する可能性はありますが、1秒あたり数個のメッセージだけが送信された場合、SignalR アプリは接続自体を処理するために、複数のサーバーにスケールアウトする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="4a26f-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="4a26f-150">SignalR アプリでは、SignalR のない web アプリよりもはるかに多くの接続リソースは使用されません。</span><span class="sxs-lookup"><span data-stu-id="4a26f-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="4a26f-151">これらの理由から、azure でホストされているすべての ASP.NET Core SignalR アプリ (App Service、Vm、コンテナーなど) に対して Azure SignalR サービスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4a26f-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="4a26f-152">詳細については、 [Azure SignalR サービスのドキュメント](/azure/azure-signalr/signalr-overview)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a26f-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="4a26f-153">Redis バックプレーン</span><span class="sxs-lookup"><span data-stu-id="4a26f-153">Redis backplane</span></span>

<span data-ttu-id="4a26f-154">[Redis](https://redis.io/)は、パブリッシュ/サブスクライブモデルを持つメッセージングシステムをサポートするメモリ内キー値ストアです。</span><span class="sxs-lookup"><span data-stu-id="4a26f-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="4a26f-155">SignalR Redis バックプレーンは、pub/sub 機能を使用して、他のサーバーにメッセージを転送します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="4a26f-156">クライアントが接続を確立すると、接続情報がバックプレーンに渡されます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="4a26f-157">サーバーは、すべてのクライアントにメッセージを送信するときに、バックプレーンに送信します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="4a26f-158">バックプレーンは、接続されているすべてのクライアントと、それらがあるサーバーを認識します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="4a26f-159">このメッセージは、各サーバーを介してすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="4a26f-160">このプロセスを次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-160">This process is illustrated in the following diagram:</span></span>

![Redis バックプレーン、1つのサーバーからすべてのクライアントに送信されたメッセージ](scale/_static/redis-backplane.png)

<span data-ttu-id="4a26f-162">Redis バックプレーンは、お客様のインフラストラクチャでホストされているアプリに推奨されるスケールアウトアプローチです。</span><span class="sxs-lookup"><span data-stu-id="4a26f-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="4a26f-163">データセンターと Azure データセンターの間に大きな接続の待機時間がある場合、低待機時間または高いスループットの要件を持つオンプレミスのアプリでは、Azure SignalR サービスが実用的な選択肢ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="4a26f-164">前述した Azure SignalR サービスの利点は、Redis バックプレーンの欠点です。</span><span class="sxs-lookup"><span data-stu-id="4a26f-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="4a26f-165">次の**両方**が当てはまる場合を除き、固定セッション ([クライアントアフィニティ](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)とも呼ばれます) が必要です。</span><span class="sxs-lookup"><span data-stu-id="4a26f-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="4a26f-166">すべてのクライアントは、Websocket**のみ**を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="4a26f-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="4a26f-167">クライアント構成で[Skipnegotiation 設定](xref:signalr/configuration#configure-additional-options)が有効になっています。</span><span class="sxs-lookup"><span data-stu-id="4a26f-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="4a26f-168">サーバーで接続が開始されると、接続はそのサーバー上にとどまります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="4a26f-169">SignalR のアプリは、送信されるメッセージが少ない場合でも、クライアントの数に基づいてスケールアウトする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="4a26f-170">SignalR アプリでは、SignalRのない web アプリよりもはるかに多くの接続リソースを使用します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="4a26f-171">Windows クライアント OS での IIS の制限事項</span><span class="sxs-lookup"><span data-stu-id="4a26f-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="4a26f-172">Windows 10 と Windows 8.x はクライアントオペレーティングシステムです。</span><span class="sxs-lookup"><span data-stu-id="4a26f-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="4a26f-173">クライアントオペレーティングシステムの IIS では、同時接続数が10個に制限されています。</span><span class="sxs-lookup"><span data-stu-id="4a26f-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> SignalR<span data-ttu-id="4a26f-174">の接続は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4a26f-174">'s connections are:</span></span>

* <span data-ttu-id="4a26f-175">一時的で、頻繁に再確立されます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="4a26f-176">使用されなくなった場合、すぐに**は破棄されません**。</span><span class="sxs-lookup"><span data-stu-id="4a26f-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="4a26f-177">上記の条件を満たすと、クライアント OS で10個の接続制限に達する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="4a26f-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="4a26f-178">クライアント OS を開発に使用する場合は、次のことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4a26f-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="4a26f-179">IIS を避けます。</span><span class="sxs-lookup"><span data-stu-id="4a26f-179">Avoid IIS.</span></span>
* <span data-ttu-id="4a26f-180">配置ターゲットとして Kestrel または IIS Express を使用します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="4a26f-181">Nginx を使用した Linux</span><span class="sxs-lookup"><span data-stu-id="4a26f-181">Linux with Nginx</span></span>

<span data-ttu-id="4a26f-182">Websocket を SignalR するには、プロキシの `Connection` と `Upgrade` ヘッダーを次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="4a26f-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="4a26f-183">詳細については、「[WebSocket プロキシとしての NGINX](https://www.nginx.com/blog/websocket-nginx/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a26f-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="4a26f-184">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="4a26f-184">Next steps</span></span>

<span data-ttu-id="4a26f-185">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a26f-185">For more information, see the following resources:</span></span>

* <span data-ttu-id="4a26f-186">[Azure SignalR サービスのドキュメント](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="4a26f-186">[Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview)</span></span>
* [<span data-ttu-id="4a26f-187">Redis バックプレーンを設定する</span><span class="sxs-lookup"><span data-stu-id="4a26f-187">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
