---
title: ASP.NET Core SignalR サポートされているプラットフォーム
author: bradygaster
description: ASP.NET Core SignalRでサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 054965921c87c1a9be27e5ddaa8a87b0fa1f4113
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146499"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a><span data-ttu-id="44876-103">ASP.NET Core SignalR サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="44876-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="44876-104">サーバー システムの要件</span><span class="sxs-lookup"><span data-stu-id="44876-104">Server system requirements</span></span>

<span data-ttu-id="44876-105">ASP.NET Core の SignalR は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートします。</span><span class="sxs-lookup"><span data-stu-id="44876-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="44876-106">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="44876-106">JavaScript client</span></span>

<span data-ttu-id="44876-107">[JavaScript クライアント](xref:signalr/javascript-client)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。</span><span class="sxs-lookup"><span data-stu-id="44876-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="44876-108">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="44876-108">Browser</span></span>                         | <span data-ttu-id="44876-109">Version</span><span class="sxs-lookup"><span data-stu-id="44876-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="44876-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44876-110">Microsoft Edge</span></span>                  | <span data-ttu-id="44876-111">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="44876-111">Current&dagger;</span></span> |
| <span data-ttu-id="44876-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44876-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="44876-113">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="44876-113">Current&dagger;</span></span> |
| <span data-ttu-id="44876-114">Google Chrome。Android を含む</span><span class="sxs-lookup"><span data-stu-id="44876-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="44876-115">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="44876-115">Current&dagger;</span></span> |
| <span data-ttu-id="44876-116">SafariiOS を含む</span><span class="sxs-lookup"><span data-stu-id="44876-116">Safari; includes iOS</span></span>            | <span data-ttu-id="44876-117">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="44876-117">Current&dagger;</span></span> |
| <span data-ttu-id="44876-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="44876-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="44876-119">11</span><span class="sxs-lookup"><span data-stu-id="44876-119">11</span></span>              |

<span data-ttu-id="44876-120">*現在*の &dagger;は、ブラウザーの最新バージョンを参照します。</span><span class="sxs-lookup"><span data-stu-id="44876-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="44876-121">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="44876-121">.NET client</span></span>

<span data-ttu-id="44876-122">[.Net クライアント](xref:signalr/dotnet-client)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。</span><span class="sxs-lookup"><span data-stu-id="44876-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="44876-123">たとえば、xamarin[開発者は](https://github.com/aspnet/Announcements/issues/305)、xamarin 8.4.0.1 以降と11.14.0.4 以降を使用して、android アプリをビルドするために SignalRを使用できます。</span><span class="sxs-lookup"><span data-stu-id="44876-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="44876-124">サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="44876-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="44876-125">その他のトランスポートはすべてのプラットフォームでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="44876-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="44876-126">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="44876-126">Java client</span></span>

<span data-ttu-id="44876-127">[Java クライアント](xref:signalr/java-client)は、java 8 以降のバージョンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="44876-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="44876-128">サポートされていないクライアント</span><span class="sxs-lookup"><span data-stu-id="44876-128">Unsupported clients</span></span>

<span data-ttu-id="44876-129">次のクライアントは使用できますが、試験的または非公式です。</span><span class="sxs-lookup"><span data-stu-id="44876-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="44876-130">現時点ではサポートされておらず、そうでない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="44876-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="44876-131">[C++client](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="44876-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="44876-132">[Swift クライアント](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="44876-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
