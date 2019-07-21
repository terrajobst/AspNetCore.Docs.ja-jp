---
title: サーバー側 ASP.NET Core Blazor のホストと展開
author: guardrex
description: ASP.NET Core を使用して Blazor サーバー側アプリをホストおよびデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 56a03ff583bf85497e2b3bacc70123845a046e3d
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892696"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="8046d-103">サーバー側 Blazor をホストおよび展開する</span><span class="sxs-lookup"><span data-stu-id="8046d-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="8046d-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8046d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8046d-105">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="8046d-105">Host configuration values</span></span>

<span data-ttu-id="8046d-106">[サーバー側のホスティング モデル](xref:blazor/hosting-models#server-side)を使用するサーバー側アプリでは、[汎用ホスト構成値](xref:fundamentals/host/generic-host#host-configuration)を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="8046d-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="8046d-107">配置</span><span class="sxs-lookup"><span data-stu-id="8046d-107">Deployment</span></span>

<span data-ttu-id="8046d-108">[サーバー側のホスティング モデル](xref:blazor/hosting-models#server-side)では、Blazor はサーバー上で ASP.NET Core アプリ内から実行されます。</span><span class="sxs-lookup"><span data-stu-id="8046d-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="8046d-109">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="8046d-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="8046d-110">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="8046d-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8046d-111">Visual Studio には **Blazor サーバー アプリ** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserverside` テンプレート)。</span><span class="sxs-lookup"><span data-stu-id="8046d-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="connection-scale-out"></a><span data-ttu-id="8046d-112">接続のスケールアウト</span><span class="sxs-lookup"><span data-stu-id="8046d-112">Connection scale out</span></span>

<span data-ttu-id="8046d-113">サーバー側 Blazor アプリでは、ユーザーごとに 1 つのアクティブな SignalR 接続が必要です。</span><span class="sxs-lookup"><span data-stu-id="8046d-113">Blazor server-side apps require one active SignalR connection for each user.</span></span> <span data-ttu-id="8046d-114">サーバー側 Blazor を運用環境に展開するには、アプリで必要になるのと同じ数のコンカレント接続をサポートするためのソリューションが必要です。</span><span class="sxs-lookup"><span data-stu-id="8046d-114">A production Blazor server-side deployment requires a solution for supporting as many concurrent connections as required by the app.</span></span> <span data-ttu-id="8046d-115">[Azure SignalR Service](/azure/azure-signalr/) では接続のスケーリングを処理でき、サーバー側 Blazor アプリ用のスケーリング ソリューションとしてお勧めです。</span><span class="sxs-lookup"><span data-stu-id="8046d-115">The [Azure SignalR Service](/azure/azure-signalr/) handles the scaling of connections and is recommended as a scaling solution for Blazor server-side apps.</span></span> <span data-ttu-id="8046d-116">詳細については、<xref:signalr/publish-to-azure-web-app> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8046d-116">For more information, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="signalr-configuration"></a><span data-ttu-id="8046d-117">SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="8046d-117">SignalR configuration</span></span>

<span data-ttu-id="8046d-118">最も一般的なサーバー側 Blazor のシナリオでは、SignalR は ASP.NET Core によって構成されます。</span><span class="sxs-lookup"><span data-stu-id="8046d-118">SignalR is configured by ASP.NET Core for the most common Blazor server-side scenarios.</span></span> <span data-ttu-id="8046d-119">カスタムの高度なシナリオについては、「[その他の技術情報](#additional-resources)」セクションにある SignalR の記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8046d-119">For custom and advanced scenarios, consult the SignalR articles in the [Additional resources](#additional-resources) section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8046d-120">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8046d-120">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="8046d-121">Azure SignalR Service のドキュメント</span><span class="sxs-lookup"><span data-stu-id="8046d-121">Azure SignalR Service Documentation</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="8046d-122">クイック スタート:SignalR Service を使用してチャット ルームを作成する</span><span class="sxs-lookup"><span data-stu-id="8046d-122">Quickstart: Create a chat room by using SignalR Service</span></span>](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="8046d-123">Azure App Service に ASP.NET Core プレビュー リリースを展開する</span><span class="sxs-lookup"><span data-stu-id="8046d-123">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
