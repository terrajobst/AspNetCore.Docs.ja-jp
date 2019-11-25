---
title: ASP.NET 4.x と ASP.NET Core の選択
author: rick-anderson
description: ASP.NET Core とASP.NET 4.x の違いと、どちらかを選択する方法について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 8b1681476f96e8613f9461c507fbb7696f888cbc
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963624"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="fa140-103">ASP.NET 4.x と ASP.NET Core の選択</span><span class="sxs-lookup"><span data-stu-id="fa140-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="fa140-104">ASP.NET Core は ASP.NET 4.x を再設計したものです。</span><span class="sxs-lookup"><span data-stu-id="fa140-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="fa140-105">この記事では、この 2 つの違いを一覧します。</span><span class="sxs-lookup"><span data-stu-id="fa140-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="fa140-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa140-106">ASP.NET Core</span></span>

<span data-ttu-id="fa140-107">ASP.NET Core は、Windows、macOS、または Linux で最新のクラウド ベースの Web アプリを構築するための、オープン ソースのクロスプラットフォーム フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="fa140-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="fa140-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="fa140-108">ASP.NET 4.x</span></span>

<span data-ttu-id="fa140-109">ASP.NET 4.x は成熟したフレームワークであり、Windows 上でエンタープライズ グレードのサーバー ベース Web アプリを構築するために必要なサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="fa140-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="fa140-110">フレームワークの選択</span><span class="sxs-lookup"><span data-stu-id="fa140-110">Framework selection</span></span>

<span data-ttu-id="fa140-111">次の表では、ASP.NET Core と ASP.NET 4.x を比較します。</span><span class="sxs-lookup"><span data-stu-id="fa140-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="fa140-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa140-112">ASP.NET Core</span></span> | <span data-ttu-id="fa140-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="fa140-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="fa140-114">Windows、macOS、Linux が対象</span><span class="sxs-lookup"><span data-stu-id="fa140-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="fa140-115">Windows が対象</span><span class="sxs-lookup"><span data-stu-id="fa140-115">Build for Windows</span></span>|
|<span data-ttu-id="fa140-116">[Razor ページ](xref:razor-pages/index)は、ASP.NET Core 2.x の時点で Web UI を作成する場合に推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="fa140-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="fa140-117">[MVC](xref:mvc/overview)、[Web API](xref:tutorials/first-web-api)、[SignalR](xref:signalr/introduction) についても参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa140-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="fa140-118">[Web Forms](/aspnet/web-forms)、[SignalR](/aspnet/signalr)、[MVC](/aspnet/mvc)、[Web API](/aspnet/web-api/)、[WebHooks](/aspnet/webhooks/)、または [Web ページ](/aspnet/web-pages)を使います</span><span class="sxs-lookup"><span data-stu-id="fa140-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="fa140-119">コンピューターごとに複数のバージョン</span><span class="sxs-lookup"><span data-stu-id="fa140-119">Multiple versions per machine</span></span>|<span data-ttu-id="fa140-120">コンピューターごとに 1 つのバージョン</span><span class="sxs-lookup"><span data-stu-id="fa140-120">One version per machine</span></span>|
|<span data-ttu-id="fa140-121">C# または F# を使い、[Visual Studio](https://visualstudio.microsoft.com/vs/)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)、または [Visual Studio Code](https://code.visualstudio.com/) で 開発</span><span class="sxs-lookup"><span data-stu-id="fa140-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="fa140-122">C#、VB、または F# を使い、[Visual Studio](https://visualstudio.microsoft.com/vs/) で開発</span><span class="sxs-lookup"><span data-stu-id="fa140-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="fa140-123">ASP.NET 4.x より高いパフォーマンス</span><span class="sxs-lookup"><span data-stu-id="fa140-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="fa140-124">よいパフォーマンス</span><span class="sxs-lookup"><span data-stu-id="fa140-124">Good performance</span></span>|
|[<span data-ttu-id="fa140-125">.NET Core ランタイムを使用する</span><span class="sxs-lookup"><span data-stu-id="fa140-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="fa140-126">.NET Framework ランタイムを使います</span><span class="sxs-lookup"><span data-stu-id="fa140-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="fa140-127">.NET Framework 上での ASP.NET Core 2.x のサポートについては、「[.NET Framework を対象とする ASP.NET Core](xref:index#target-framework)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa140-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="fa140-128">ASP.NET Core のシナリオ</span><span class="sxs-lookup"><span data-stu-id="fa140-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="fa140-129">Web サイト</span><span class="sxs-lookup"><span data-stu-id="fa140-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="fa140-130">API</span><span class="sxs-lookup"><span data-stu-id="fa140-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="fa140-131">リアルタイム</span><span class="sxs-lookup"><span data-stu-id="fa140-131">Real-time</span></span>](xref:signalr/index)
* [<span data-ttu-id="fa140-132">Azure に ASP.NET Core アプリをデプロイする</span><span class="sxs-lookup"><span data-stu-id="fa140-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="fa140-133">ASP.NET 4.x のシナリオ</span><span class="sxs-lookup"><span data-stu-id="fa140-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="fa140-134">Web サイト</span><span class="sxs-lookup"><span data-stu-id="fa140-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="fa140-135">API</span><span class="sxs-lookup"><span data-stu-id="fa140-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="fa140-136">リアルタイム</span><span class="sxs-lookup"><span data-stu-id="fa140-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="fa140-137">Azure 内で ASP.NET 4.x Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="fa140-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="fa140-138">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fa140-138">Additional resources</span></span>

* [<span data-ttu-id="fa140-139">ASP.NET の概要</span><span class="sxs-lookup"><span data-stu-id="fa140-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="fa140-140">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="fa140-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
