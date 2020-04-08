---
title: ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: Azure でホストされる ASP.NET Core アプリの DevOps パイプラインの構築に関するエンドツーエンドのガイダンスを提供するガイド。
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
uid: azure/devops/index
ms.openlocfilehash: f45bb2a5dd4b3d1a820085ede7ce3219045ed80b
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78644246"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="bb2ef-103">ASP.NET Core および Azure を使用した DevOps</span><span class="sxs-lookup"><span data-stu-id="bb2ef-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="bb2ef-104">[![カバーの画像](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="bb2ef-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="bb2ef-105">[Cam Soper](https://twitter.com/camsoper) および [Scott Addie](https://twitter.com/scottaddie) 著</span><span class="sxs-lookup"><span data-stu-id="bb2ef-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="bb2ef-106">このガイドは、[ダウンロード可能 PDF 電子ブック](https://aka.ms/devopsbook)として入手できます。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="bb2ef-107">ようこそ</span><span class="sxs-lookup"><span data-stu-id="bb2ef-107">Welcome</span></span> 

<span data-ttu-id="bb2ef-108">.NET 向けの Azure 開発ライフサイクル ガイドへようこそ。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="bb2ef-109">このガイドでは .NET のツールとプロセスを使用する Azure に関する開発ライフサイクルの構築に関する基本概念を紹介します。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="bb2ef-110">このガイドを完了すると、進化した DevOps ツールチェーンの利点が得られます。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="bb2ef-111">このガイドの対象ユーザー</span><span class="sxs-lookup"><span data-stu-id="bb2ef-111">Who this guide is for</span></span>

<span data-ttu-id="bb2ef-112">熟練した ASP.NET Core 開発者 (200-300 レベル) である必要があります。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="bb2ef-113">Azure に関する知識は必要ありません。それについてはこのガイドで説明します。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="bb2ef-114">このガイドは、開発よりも操作に注力する DevOps エンジニアにも役立つ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="bb2ef-115">このガイドは、Windows 開発者を対象としています。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-115">This guide targets Windows developers.</span></span> <span data-ttu-id="bb2ef-116">ただし、.NET Core によって Linux と macOS が完全にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="bb2ef-117">このガイドを Linux/macOS で使用する場合は、吹き出しで Linux/macOS との違いを確認してください。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="bb2ef-118">このガイドに含まれないもの</span><span class="sxs-lookup"><span data-stu-id="bb2ef-118">What this guide doesn't cover</span></span>

<span data-ttu-id="bb2ef-119">このガイドは、.NET 開発者向けのエンドツーエンドの継続的デプロイに焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="bb2ef-120">Azure のすべてを網羅するガイドではなく、Azure サービス向けの .NET API について幅広く取り上げるものでもありません。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="bb2ef-121">特に焦点を当てるのは、継続的インテグレーション、デプロイ、監視、デバッグについてです。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="bb2ef-122">このガイドの終盤には、次のステップとしてお勧めする内容を紹介します。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="bb2ef-123">その内容には ASP.NET Core 開発者に役立つ Azure プラットフォーム サービスも含まれています。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="bb2ef-124">このガイドの内容</span><span class="sxs-lookup"><span data-stu-id="bb2ef-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="bb2ef-125">ツールとダウンロード</span><span class="sxs-lookup"><span data-stu-id="bb2ef-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="bb2ef-126">このガイドで使用するツールを取得できる場所について説明します。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="bb2ef-127">App Service にデプロイする</span><span class="sxs-lookup"><span data-stu-id="bb2ef-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="bb2ef-128">Azure App Service に ASP.NET Core アプリをデプロイするためのさまざまな方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="bb2ef-129">継続的インテグレーションと継続的デプロイ</span><span class="sxs-lookup"><span data-stu-id="bb2ef-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="bb2ef-130">GitHub、Azure DevOps Services、Azure で自分の ASP.NET Core アプリにエンドツーエンドの継続的インテグレーションと継続的デプロイ ソリューションを構築します。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="bb2ef-131">監視とデバッグ</span><span class="sxs-lookup"><span data-stu-id="bb2ef-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="bb2ef-132">Azure のツールを使用して、アプリケーションの監視、トラブルシューティング、調整を行います。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="bb2ef-133">次の手順</span><span class="sxs-lookup"><span data-stu-id="bb2ef-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="bb2ef-134">Azure を学習する ASP.NET Core 開発者向けのその他のラーニング パス。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="bb2ef-135">その他の入門資料</span><span class="sxs-lookup"><span data-stu-id="bb2ef-135">Additional introductory reading</span></span>

<span data-ttu-id="bb2ef-136">クラウド コンピューティングを初めて使用する場合は、基礎について次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bb2ef-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="bb2ef-137">クラウド コンピューティングとは</span><span class="sxs-lookup"><span data-stu-id="bb2ef-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="bb2ef-138">クラウド コンピューティングの例</span><span class="sxs-lookup"><span data-stu-id="bb2ef-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="bb2ef-139">IaaS とは</span><span class="sxs-lookup"><span data-stu-id="bb2ef-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="bb2ef-140">PaaS とは</span><span class="sxs-lookup"><span data-stu-id="bb2ef-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
