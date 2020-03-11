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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655148"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="cda8b-103">SignalR でサポートされているプラットフォームの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cda8b-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="cda8b-104">サーバー システムの要件</span><span class="sxs-lookup"><span data-stu-id="cda8b-104">Server system requirements</span></span>

<span data-ttu-id="cda8b-105">ASP.NET Core の SignalR は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cda8b-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="cda8b-106">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="cda8b-106">JavaScript client</span></span>

<span data-ttu-id="cda8b-107">[JavaScript クライアント](xref:signalr/javascript-client)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cda8b-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="cda8b-108">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="cda8b-108">Browser</span></span>                         | <span data-ttu-id="cda8b-109">バージョン</span><span class="sxs-lookup"><span data-stu-id="cda8b-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="cda8b-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="cda8b-110">Microsoft Edge</span></span>                  | <span data-ttu-id="cda8b-111">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="cda8b-111">Current&dagger;</span></span> |
| <span data-ttu-id="cda8b-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="cda8b-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="cda8b-113">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="cda8b-113">Current&dagger;</span></span> |
| <span data-ttu-id="cda8b-114">Google Chrome。Android を含む</span><span class="sxs-lookup"><span data-stu-id="cda8b-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="cda8b-115">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="cda8b-115">Current&dagger;</span></span> |
| <span data-ttu-id="cda8b-116">SafariiOS を含む</span><span class="sxs-lookup"><span data-stu-id="cda8b-116">Safari; includes iOS</span></span>            | <span data-ttu-id="cda8b-117">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="cda8b-117">Current&dagger;</span></span> |
| <span data-ttu-id="cda8b-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="cda8b-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="cda8b-119">11</span><span class="sxs-lookup"><span data-stu-id="cda8b-119">11</span></span>              |

<span data-ttu-id="cda8b-120">*現在*の &dagger;は、ブラウザーの最新バージョンを参照します。</span><span class="sxs-lookup"><span data-stu-id="cda8b-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="cda8b-121">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="cda8b-121">.NET client</span></span>

<span data-ttu-id="cda8b-122">[.Net クライアント](xref:signalr/dotnet-client)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cda8b-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="cda8b-123">たとえば、xamarin[開発者は](https://github.com/aspnet/Announcements/issues/305)、xamarin 8.4.0.1 以降と11.14.0.4 以降を使用して、android アプリをビルドするために SignalRを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cda8b-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="cda8b-124">サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="cda8b-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="cda8b-125">その他のトランスポートはすべてのプラットフォームでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cda8b-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="cda8b-126">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="cda8b-126">Java client</span></span>

<span data-ttu-id="cda8b-127">[Java クライアント](xref:signalr/java-client)は、java 8 以降のバージョンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cda8b-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="cda8b-128">サポートされていないクライアント</span><span class="sxs-lookup"><span data-stu-id="cda8b-128">Unsupported clients</span></span>

<span data-ttu-id="cda8b-129">次のクライアントは使用できますが、試験的または非公式です。</span><span class="sxs-lookup"><span data-stu-id="cda8b-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="cda8b-130">現時点ではサポートされておらず、そうでない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="cda8b-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="cda8b-131">[C++client](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="cda8b-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="cda8b-132">[Swift クライアント](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="cda8b-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
