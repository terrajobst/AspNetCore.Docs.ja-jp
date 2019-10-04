---
title: SignalR クライアントの機能
author: bradygaster
description: さまざまな ASP.NET Core SignalR クライアントでサポートされている機能について説明します。
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925351"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="84720-103">SignalR クライアント機能の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84720-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="84720-104">機能の配布</span><span class="sxs-lookup"><span data-stu-id="84720-104">Feature distribution</span></span>

<span data-ttu-id="84720-105">次の表は、リアルタイムサポートを提供するクライアントの機能とサポートを示しています。</span><span class="sxs-lookup"><span data-stu-id="84720-105">The table below shows the features and support for the clients that offer real-time support.</span></span> <span data-ttu-id="84720-106">各機能について、この機能をサポートする*最小*バージョンが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="84720-106">For each feature, the *minimum* version supporting this feature is listed.</span></span> <span data-ttu-id="84720-107">バージョンが表示されない場合、この機能はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="84720-107">If no version is listed, the feature isn't supported.</span></span>

| <span data-ttu-id="84720-108">機能</span><span class="sxs-lookup"><span data-stu-id="84720-108">Feature</span></span> | <span data-ttu-id="84720-109">.NET</span><span class="sxs-lookup"><span data-stu-id="84720-109">.NET</span></span> | <span data-ttu-id="84720-110">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84720-110">JavaScript</span></span> | <span data-ttu-id="84720-111">Java</span><span class="sxs-lookup"><span data-stu-id="84720-111">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="84720-112">Azure SignalR サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="84720-112">Azure SignalR Service Support</span></span> |<span data-ttu-id="84720-113">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-113">1.0.0</span></span>|<span data-ttu-id="84720-114">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-114">1.0.0</span></span>|<span data-ttu-id="84720-115">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-115">1.0.0</span></span>|
| [<span data-ttu-id="84720-116">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="84720-116">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="84720-117">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-117">1.0.0</span></span>|<span data-ttu-id="84720-118">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-118">1.0.0</span></span>|<span data-ttu-id="84720-119">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-119">1.0.0</span></span>|
| [<span data-ttu-id="84720-120">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="84720-120">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="84720-121">3.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-121">3.0.0</span></span>|<span data-ttu-id="84720-122">3.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-122">3.0.0</span></span>|<span data-ttu-id="84720-123">3.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-123">3.0.0</span></span>|
| <span data-ttu-id="84720-124">自動再接続 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)、 [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="84720-124">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="84720-125">3.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-125">3.0.0</span></span>|<span data-ttu-id="84720-126">3.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-126">3.0.0</span></span>|<span data-ttu-id="84720-127">❌</span><span class="sxs-lookup"><span data-stu-id="84720-127">❌</span></span>|
| <span data-ttu-id="84720-128">Websocket トランスポート</span><span class="sxs-lookup"><span data-stu-id="84720-128">WebSockets Transport</span></span> |<span data-ttu-id="84720-129">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-129">1.0.0</span></span>|<span data-ttu-id="84720-130">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-130">1.0.0</span></span>|<span data-ttu-id="84720-131">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-131">1.0.0</span></span>|
| <span data-ttu-id="84720-132">サーバー送信イベントトランスポート</span><span class="sxs-lookup"><span data-stu-id="84720-132">Server-Sent Events Transport</span></span> |<span data-ttu-id="84720-133">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-133">1.0.0</span></span>|<span data-ttu-id="84720-134">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-134">1.0.0</span></span>|<span data-ttu-id="84720-135">❌</span><span class="sxs-lookup"><span data-stu-id="84720-135">❌</span></span>|
| <span data-ttu-id="84720-136">長いポーリングトランスポート</span><span class="sxs-lookup"><span data-stu-id="84720-136">Long Polling Transport</span></span> |<span data-ttu-id="84720-137">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-137">1.0.0</span></span>|<span data-ttu-id="84720-138">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-138">1.0.0</span></span>|<span data-ttu-id="84720-139">3.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-139">3.0.0</span></span>|
| <span data-ttu-id="84720-140">JSON ハブプロトコル</span><span class="sxs-lookup"><span data-stu-id="84720-140">JSON Hub Protocol</span></span> |<span data-ttu-id="84720-141">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-141">1.0.0</span></span>|<span data-ttu-id="84720-142">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-142">1.0.0</span></span>|<span data-ttu-id="84720-143">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-143">1.0.0</span></span>|
| <span data-ttu-id="84720-144">MessagePack ハブ プロトコル</span><span class="sxs-lookup"><span data-stu-id="84720-144">MessagePack Hub Protocol</span></span> |<span data-ttu-id="84720-145">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-145">1.0.0</span></span>|<span data-ttu-id="84720-146">1.0.0</span><span class="sxs-lookup"><span data-stu-id="84720-146">1.0.0</span></span>|<span data-ttu-id="84720-147">❌</span><span class="sxs-lookup"><span data-stu-id="84720-147">❌</span></span>|

<span data-ttu-id="84720-148">Java クライアントでの自動再接続のサポートは[、microsoft の問題追跡ツール](https://github.com/aspnet/AspNetCore/issues/8711)で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="84720-148">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84720-149">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="84720-149">Additional resources</span></span>

* [<span data-ttu-id="84720-150">ASP.NET Core の SignalR 概要</span><span class="sxs-lookup"><span data-stu-id="84720-150">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="84720-151">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="84720-151">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="84720-152">ハブ</span><span class="sxs-lookup"><span data-stu-id="84720-152">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="84720-153">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="84720-153">JavaScript client</span></span>](xref:signalr/javascript-client)
