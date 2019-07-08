---
title: ASP.NET Core でのサードパーティ コンテナーによるミドルウェアのアクティブ化
author: guardrex
description: ASP.NET Core で、ファクトリベースのアクティブ化とサードパーティ コンテナーによる厳密に型指定されたミドルウェアを使用する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/03/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 4bc99b4c336aba611287c9fbe03d4252f8abee5b
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561645"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="a3edd-103">ASP.NET Core でのサードパーティ コンテナーによるミドルウェアのアクティブ化</span><span class="sxs-lookup"><span data-stu-id="a3edd-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="a3edd-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a3edd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a3edd-105">この記事では、<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> と <xref:Microsoft.AspNetCore.Http.IMiddleware> を、サードパーティ コンテナーによる[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントとして使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a3edd-105">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="a3edd-106">`IMiddlewareFactory` と `IMiddleware` の概要については、「<xref:fundamentals/middleware/extensibility>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a3edd-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="a3edd-107">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a3edd-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a3edd-108">このサンプル アプリでは、`IMiddlewareFactory` の実装である `SimpleInjectorMiddlewareFactory` によるミドルウェアのアクティブ化を示します。</span><span class="sxs-lookup"><span data-stu-id="a3edd-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="a3edd-109">このサンプルでは、[Simple Injector](https://simpleinjector.org) 依存関係の挿入 (DI) コンテナーを使用しています。</span><span class="sxs-lookup"><span data-stu-id="a3edd-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="a3edd-110">サンプルのミドルウェアの実装は、クエリ文字列パラメーターで提供された値を記録します (`key`)。</span><span class="sxs-lookup"><span data-stu-id="a3edd-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="a3edd-111">ミドルウェアは、メモリ内データベースにクエリ文字列値を記録するのに、挿入されたデータベース コンテキスト (スコープ化されたサービス) を使用します。</span><span class="sxs-lookup"><span data-stu-id="a3edd-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="a3edd-112">このサンプル アプリでは、デモンストレーション目的でのみ [Simple Injector](https://github.com/simpleinjector/SimpleInjector) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="a3edd-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="a3edd-113">Simple Injector の使用を推奨するものではありません。</span><span class="sxs-lookup"><span data-stu-id="a3edd-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="a3edd-114">Simple Injector のドキュメントと GitHub Issues に記載されているミドルウェアのアクティブ化方法は、Simple Injector の保守担当者たちから推奨されています。</span><span class="sxs-lookup"><span data-stu-id="a3edd-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="a3edd-115">詳細については、[Simple Injector のドキュメント](https://simpleinjector.readthedocs.io/en/latest/index.html)と [Simple Injector の GitHub リポジトリ](https://github.com/simpleinjector/SimpleInjector)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a3edd-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="a3edd-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="a3edd-116">IMiddlewareFactory</span></span>

<span data-ttu-id="a3edd-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a3edd-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="a3edd-118">サンプル アプリでは、`SimpleInjectorActivatedMiddleware` インスタンスを作成するミドルウェア ファクトリが実装されています。</span><span class="sxs-lookup"><span data-stu-id="a3edd-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="a3edd-119">このミドルウェア ファクトリでは、Simple Injector コンテナーを使用してミドルウェアを解決しています。</span><span class="sxs-lookup"><span data-stu-id="a3edd-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="a3edd-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="a3edd-120">IMiddleware</span></span>

<span data-ttu-id="a3edd-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="a3edd-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="a3edd-122">`IMiddlewareFactory` の実装によってアクティブ化されるミドルウェア (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="a3edd-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="a3edd-123">ミドルウェア (*Middleware/MiddlewareExtensions.cs*) の拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="a3edd-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="a3edd-124">`Startup.ConfigureServices` はいくつかのタスクを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a3edd-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="a3edd-125">Simple Injector コンテナーを設定します。</span><span class="sxs-lookup"><span data-stu-id="a3edd-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="a3edd-126">ファクトリとミドルウェアを登録します。</span><span class="sxs-lookup"><span data-stu-id="a3edd-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="a3edd-127">Simple Injector コンテナーからアプリのデータベース コンテキストを利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a3edd-127">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="a3edd-128">ミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="a3edd-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="a3edd-129">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a3edd-129">Additional resources</span></span>

* [<span data-ttu-id="a3edd-130">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="a3edd-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="a3edd-131">ファクトリ ベースのミドルウェアのアクティブ化</span><span class="sxs-lookup"><span data-stu-id="a3edd-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="a3edd-132">Simple Injector の GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="a3edd-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="a3edd-133">Simple Injector のドキュメント</span><span class="sxs-lookup"><span data-stu-id="a3edd-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
