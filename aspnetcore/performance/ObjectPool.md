---
title: ASP.NET Core で ObjectPool でオブジェクトを再利用
author: rick-anderson
description: ObjectPool を使用して ASP.NET Core アプリでのパフォーマンスを高めるためのヒント。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815526"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="f99f9-103">ASP.NET Core で ObjectPool でオブジェクトを再利用</span><span class="sxs-lookup"><span data-stu-id="f99f9-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="f99f9-104">によって[Steve Gordon](https://twitter.com/stevejgordon)、 [Ryan Nowak](https://github.com/rynowak)、および[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f99f9-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f99f9-105"><xref:Microsoft.Extensions.ObjectPool> 収集されたガベージ オブジェクトではなく、再利用するためのメモリ内オブジェクトのグループを維持することをサポートする ASP.NET Core インフラストラクチャの一部です。</span><span class="sxs-lookup"><span data-stu-id="f99f9-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="f99f9-106">管理されているオブジェクトがある場合は、オブジェクト プールを使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="f99f9-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="f99f9-107">負荷の高いを割り当てまたは初期化します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="f99f9-108">いくつか制限されているリソースを表します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-108">Represent some limited resource.</span></span>
- <span data-ttu-id="f99f9-109">予測的かつ頻繁に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f99f9-109">Used predictably and frequently.</span></span>

<span data-ttu-id="f99f9-110">たとえば、ASP.NET Core フレームワークを使用してオブジェクト プールいくつかの場所で再利用する<xref:System.Text.StringBuilder>インスタンス。</span><span class="sxs-lookup"><span data-stu-id="f99f9-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="f99f9-111">`StringBuilder` 割り当てられ、文字データを保持するバッファーを管理します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="f99f9-112">ASP.NET Core を使用して定期的に`StringBuilder`機能を実装して、再利用することにより、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="f99f9-113">オブジェクト プールは、パフォーマンスを向上することでは常に。</span><span class="sxs-lookup"><span data-stu-id="f99f9-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="f99f9-114">オブジェクトの初期化コストが高でない限りは、通常、プールからオブジェクトの取得に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="f99f9-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="f99f9-115">プールで管理されるオブジェクトは、プールが割り当て解除済みになるまで割り当て解除済みではありません。</span><span class="sxs-lookup"><span data-stu-id="f99f9-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="f99f9-116">オブジェクトは、現実的なシナリオを使用して、アプリまたはライブラリのパフォーマンス データを収集した後でのみプールを使用します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="f99f9-117">**警告:`ObjectPool`を実装していない`IDisposable`します。破棄が必要な型で使用することお勧めしません。**</span><span class="sxs-lookup"><span data-stu-id="f99f9-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="f99f9-118">**注:割り当てるにはオブジェクトの数に制限を設定しない、ObjectPool が保持されますオブジェクトの数に制限がかかります。**</span><span class="sxs-lookup"><span data-stu-id="f99f9-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="f99f9-119">概念</span><span class="sxs-lookup"><span data-stu-id="f99f9-119">Concepts</span></span>

<span data-ttu-id="f99f9-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -基本的なオブジェクト プールの抽象化します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="f99f9-121">取得し、オブジェクトを返すために使用します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-121">Used to get and return objects.</span></span>

<span data-ttu-id="f99f9-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -このオブジェクトを作成する方法と方法をカスタマイズする機能を実装*リセット*プールに返されるとします。</span><span class="sxs-lookup"><span data-stu-id="f99f9-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="f99f9-123">これは、オブジェクト プールを直接構築することに渡すことができます.OR</span><span class="sxs-lookup"><span data-stu-id="f99f9-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="f99f9-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> オブジェクト プールを作成するためのファクトリとして機能します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="f99f9-125">ObjectPool は、複数の方法でのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f99f9-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="f99f9-126">プールをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-126">Instantiating a pool.</span></span>
* <span data-ttu-id="f99f9-127">内のプールを登録する[依存関係の注入](xref:fundamentals/dependency-injection)(DI) としてインスタンス。</span><span class="sxs-lookup"><span data-stu-id="f99f9-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="f99f9-128">登録、 `ObjectPoolProvider<>` DI およびファクトリとして使用します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="f99f9-129">ObjectPool を使用する方法</span><span class="sxs-lookup"><span data-stu-id="f99f9-129">How to use ObjectPool</span></span>

<span data-ttu-id="f99f9-130">呼び出す<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>オブジェクトを取得し、<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*>オブジェクトを取得します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="f99f9-131">要件のすべてのオブジェクトを返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="f99f9-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="f99f9-132">オブジェクトを返さない、ガベージ コレクションことができます。</span><span class="sxs-lookup"><span data-stu-id="f99f9-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="f99f9-133">ObjectPool サンプル</span><span class="sxs-lookup"><span data-stu-id="f99f9-133">ObjectPool sample</span></span>

<span data-ttu-id="f99f9-134">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-134">The following code:</span></span>

* <span data-ttu-id="f99f9-135">追加`ObjectPoolProvider`を[依存関係の注入](xref:fundamentals/dependency-injection)(DI) コンテナー。</span><span class="sxs-lookup"><span data-stu-id="f99f9-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="f99f9-136">設定を追加して`ObjectPool<StringBuilder>`DI コンテナーにします。</span><span class="sxs-lookup"><span data-stu-id="f99f9-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="f99f9-137">追加、`BirthdayMiddleware`します。</span><span class="sxs-lookup"><span data-stu-id="f99f9-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="f99f9-138">次のコードを実装します。 `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="f99f9-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
