---
title: ASP.NET Core の分散キャッシュ タグ ヘルパー
author: pkellner
description: 分散キャッシュ タグ ヘルパーを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 4e4d383bac67c73bad8b0a31b9ceb9452251761b
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856196"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="eec6a-103">ASP.NET Core の分散キャッシュ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="eec6a-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="eec6a-104">作成者: [Peter Kellner](https://peterkellner.net)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eec6a-104">By [Peter Kellner](https://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eec6a-105">分散キャッシュ タグ ヘルパーは、ASP.NET Core アプリの内容を分散キャッシュ ソースにキャッシュすることによって、アプリのパフォーマンスを大幅に改善する機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="eec6a-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="eec6a-106">タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="eec6a-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="eec6a-107">分散キャッシュ タグ ヘルパーは、キャッシュ タグ ヘルパーと同じ基本クラスから継承されます。</span><span class="sxs-lookup"><span data-stu-id="eec6a-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="eec6a-108">すべての[キャッシュ タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)属性は分散タグ ヘルパーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="eec6a-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="eec6a-109">分散キャッシュ タグ ヘルパーでは、[コンストラクターの挿入](xref:fundamentals/dependency-injection#constructor-injection-behavior)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="eec6a-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="eec6a-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスは、分散キャッシュ タグ ヘルパーのコンストラクターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="eec6a-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="eec6a-111">`Startup.ConfigureServices` (*Startup.cs*) に `IDistributedCache` の具体的な実装が作成されていない場合、分散キャッシュ タグ ヘルパーでは、キャッシュされたデータの格納に[キャッシュ タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)と同じメモリ内プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="eec6a-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="eec6a-112">分散キャッシュ タグ ヘルパーの属性</span><span class="sxs-lookup"><span data-stu-id="eec6a-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="eec6a-113">キャッシュ タグ ヘルパーと共有される属性</span><span class="sxs-lookup"><span data-stu-id="eec6a-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="eec6a-114">分散キャッシュ タグ ヘルパーは、キャッシュ タグ ヘルパーと同じクラスから継承されます。</span><span class="sxs-lookup"><span data-stu-id="eec6a-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="eec6a-115">これらの属性に関する説明については、[キャッシュ タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="eec6a-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="eec6a-116">name</span><span class="sxs-lookup"><span data-stu-id="eec6a-116">name</span></span>

| <span data-ttu-id="eec6a-117">属性の型</span><span class="sxs-lookup"><span data-stu-id="eec6a-117">Attribute Type</span></span> | <span data-ttu-id="eec6a-118">例</span><span class="sxs-lookup"><span data-stu-id="eec6a-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="eec6a-119">String</span><span class="sxs-lookup"><span data-stu-id="eec6a-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="eec6a-120">`name` が必要です。</span><span class="sxs-lookup"><span data-stu-id="eec6a-120">`name` is required.</span></span> <span data-ttu-id="eec6a-121">格納された各キャッシュ インスタンスごとにキーとして `name` 属性が使用されます。</span><span class="sxs-lookup"><span data-stu-id="eec6a-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="eec6a-122">キャッシュ タグ ヘルパーは Razor ページ名と、Razor ページ内での位置に基づいて各インスタンスにキャッシュ キーを割り当てますが、分散キャッシュ タグ ヘルパーのキーが基準とするのは属性 `name` のみです。</span><span class="sxs-lookup"><span data-stu-id="eec6a-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="eec6a-123">例:</span><span class="sxs-lookup"><span data-stu-id="eec6a-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="eec6a-124">分散キャッシュ タグ ヘルパー IDistributedCache の実装</span><span class="sxs-lookup"><span data-stu-id="eec6a-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="eec6a-125">ASP.NET Core には <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の 2 つの実装が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="eec6a-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="eec6a-126">1 つは SQL Server を、もう 1 つは Redis をベースにしています。</span><span class="sxs-lookup"><span data-stu-id="eec6a-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="eec6a-127">これらの実装の詳細については、<xref:performance/caching/distributed> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eec6a-127">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="eec6a-128">どちらの実装も、`Startup` での `IDistributedCache` インスタンスの設定を伴います。</span><span class="sxs-lookup"><span data-stu-id="eec6a-128">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="eec6a-129">`IDistributedCache` のいずれかの具体的な実装の使用に、明確に関連付けられている属性はありません。</span><span class="sxs-lookup"><span data-stu-id="eec6a-129">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eec6a-130">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="eec6a-130">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
