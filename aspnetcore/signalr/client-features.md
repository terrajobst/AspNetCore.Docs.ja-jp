---
title: SignalR クライアントの機能
author: bradygaster
description: さまざまな ASP.NET Core SignalR クライアントでサポートされている機能について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301187"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="72a91-103">SignalR クライアント機能の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72a91-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="72a91-104">機能の配布</span><span class="sxs-lookup"><span data-stu-id="72a91-104">Feature distribution</span></span>

<span data-ttu-id="72a91-105">次の表は、リアルタイムサポートを提供するクライアントの機能とサポートを示しています。</span><span class="sxs-lookup"><span data-stu-id="72a91-105">The table below shows the features and support for the clients that offer real-time support.</span></span>

| <span data-ttu-id="72a91-106">機能</span><span class="sxs-lookup"><span data-stu-id="72a91-106">Feature</span></span> | <span data-ttu-id="72a91-107">.NET</span><span class="sxs-lookup"><span data-stu-id="72a91-107">.NET</span></span> | <span data-ttu-id="72a91-108">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72a91-108">JavaScript</span></span> | <span data-ttu-id="72a91-109">Java</span><span class="sxs-lookup"><span data-stu-id="72a91-109">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="72a91-110">Azure SignalR サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="72a91-110">Azure SignalR Service Support</span></span> |<span data-ttu-id="72a91-111">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-111">✔</span></span>|<span data-ttu-id="72a91-112">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-112">✔</span></span>|<span data-ttu-id="72a91-113">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-113">✔</span></span>|
| [<span data-ttu-id="72a91-114">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="72a91-114">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="72a91-115">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-115">✔</span></span>|<span data-ttu-id="72a91-116">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-116">✔</span></span>|<span data-ttu-id="72a91-117">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-117">✔</span></span>|
| [<span data-ttu-id="72a91-118">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="72a91-118">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="72a91-119">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-119">✔</span></span>|<span data-ttu-id="72a91-120">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-120">✔</span></span>|<span data-ttu-id="72a91-121">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-121">✔</span></span>|
| <span data-ttu-id="72a91-122">自動再接続 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)、 [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="72a91-122">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="72a91-123">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-123">✔</span></span>|<span data-ttu-id="72a91-124">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-124">✔</span></span>| |
| <span data-ttu-id="72a91-125">Websocket トランスポート</span><span class="sxs-lookup"><span data-stu-id="72a91-125">WebSockets Transport</span></span> |<span data-ttu-id="72a91-126">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-126">✔</span></span>|<span data-ttu-id="72a91-127">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-127">✔</span></span>|<span data-ttu-id="72a91-128">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-128">✔</span></span>|
| <span data-ttu-id="72a91-129">サーバー送信イベントトランスポート</span><span class="sxs-lookup"><span data-stu-id="72a91-129">Server-Sent Events Transport</span></span> |<span data-ttu-id="72a91-130">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-130">✔</span></span>|<span data-ttu-id="72a91-131">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-131">✔</span></span>| |
| <span data-ttu-id="72a91-132">長いポーリングトランスポート</span><span class="sxs-lookup"><span data-stu-id="72a91-132">Long Polling Transport</span></span> |<span data-ttu-id="72a91-133">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-133">✔</span></span>|<span data-ttu-id="72a91-134">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-134">✔</span></span>|<span data-ttu-id="72a91-135">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-135">✔</span></span>|
| <span data-ttu-id="72a91-136">JSON ハブプロトコル</span><span class="sxs-lookup"><span data-stu-id="72a91-136">JSON Hub Protocol</span></span> |<span data-ttu-id="72a91-137">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-137">✔</span></span>|<span data-ttu-id="72a91-138">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-138">✔</span></span>|<span data-ttu-id="72a91-139">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-139">✔</span></span>|
| <span data-ttu-id="72a91-140">MessagePack ハブ プロトコル</span><span class="sxs-lookup"><span data-stu-id="72a91-140">MessagePack Hub Protocol</span></span> |<span data-ttu-id="72a91-141">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-141">✔</span></span>|<span data-ttu-id="72a91-142">✔</span><span class="sxs-lookup"><span data-stu-id="72a91-142">✔</span></span>| |

<span data-ttu-id="72a91-143">Java クライアントでの自動再接続のサポートは[、microsoft の問題追跡ツール](https://github.com/aspnet/AspNetCore/issues/8711)で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="72a91-143">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72a91-144">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="72a91-144">Additional resources</span></span>

* [<span data-ttu-id="72a91-145">ASP.NET Core の SignalR 概要</span><span class="sxs-lookup"><span data-stu-id="72a91-145">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="72a91-146">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="72a91-146">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="72a91-147">ハブ</span><span class="sxs-lookup"><span data-stu-id="72a91-147">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="72a91-148">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="72a91-148">JavaScript client</span></span>](xref:signalr/javascript-client)
