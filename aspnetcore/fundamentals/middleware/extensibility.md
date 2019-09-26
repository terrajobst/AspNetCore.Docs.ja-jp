---
title: ASP.NET Core でのファクトリ ベースのミドルウェアのアクティブ化
author: guardrex
description: ASP.NET Core で、ファクトリ ベースの厳密に型指定されたミドルウェアをアクティブ化する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 17018d2dd20ed7b26bd0aa1095fa720a73f77261
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186943"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="16e2d-103">ASP.NET Core でのファクトリ ベースのミドルウェアのアクティブ化</span><span class="sxs-lookup"><span data-stu-id="16e2d-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="16e2d-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="16e2d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="16e2d-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> は、[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントです。</span><span class="sxs-lookup"><span data-stu-id="16e2d-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="16e2d-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 拡張メソッドでは、ミドルウェアの登録済みの型で <xref:Microsoft.AspNetCore.Http.IMiddleware> が実装されているかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="16e2d-107">実装されている場合、規則に基づくミドルウェアのライセンス認証ロジックを使用する代わりに、コンテナーに登録されている <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> インスタンスが <xref:Microsoft.AspNetCore.Http.IMiddleware> の実装の解決に使用されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-107">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="16e2d-108">ミドルウェアは、アプリのサービス コンテナーで、[スコープ サービスまたは一時的サービス](xref:fundamentals/dependency-injection#service-lifetimes)として登録されています。</span><span class="sxs-lookup"><span data-stu-id="16e2d-108">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="16e2d-109">利点:</span><span class="sxs-lookup"><span data-stu-id="16e2d-109">Benefits:</span></span>

* <span data-ttu-id="16e2d-110">クライアント要求ごとにアクティブ化 (スコープ サービスの挿入)</span><span class="sxs-lookup"><span data-stu-id="16e2d-110">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="16e2d-111">ミドルウェアの厳密な型指定</span><span class="sxs-lookup"><span data-stu-id="16e2d-111">Strong typing of middleware</span></span>

<span data-ttu-id="16e2d-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> はクライアント要求 (接続) ごとにアクティブ化されているので、スコープ サービスをミドルウェアのコンストラクターに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="16e2d-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="16e2d-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="16e2d-114">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="16e2d-114">IMiddleware</span></span>

<span data-ttu-id="16e2d-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="16e2d-116">要求は、[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) メソッドによって処理され、ミドルウェアの実行を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-116">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="16e2d-117">規則でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="16e2d-117">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="16e2d-118"><xref:Microsoft.AspNetCore.Http.MiddlewareFactory> でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="16e2d-118">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="16e2d-119">ミドルウェア用に拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-119">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="16e2d-120"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> では、ファクトリでアクティブ化されたミドルウェアにオブジェクトを渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="16e2d-120">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="16e2d-121">ファクトリでアクティブ化されたミドルウェアは、`Startup.ConfigureServices` の組み込みのコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-121">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="16e2d-122">この 2 つのミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-122">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="16e2d-123">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="16e2d-123">IMiddlewareFactory</span></span>

<span data-ttu-id="16e2d-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="16e2d-125">ミドルウェア ファクトリの実装は、スコープ化されたサービスとして、コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-125">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="16e2d-126">既定の <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> の実装である <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> は、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="16e2d-126">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="16e2d-127"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> は、[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントです。</span><span class="sxs-lookup"><span data-stu-id="16e2d-127"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="16e2d-128"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 拡張メソッドでは、ミドルウェアの登録済みの型で <xref:Microsoft.AspNetCore.Http.IMiddleware> が実装されているかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-128"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="16e2d-129">実装されている場合、規則に基づくミドルウェアのライセンス認証ロジックを使用する代わりに、コンテナーに登録されている <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> インスタンスが <xref:Microsoft.AspNetCore.Http.IMiddleware> の実装の解決に使用されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-129">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="16e2d-130">ミドルウェアは、アプリのサービス コンテナーで、[スコープ サービスまたは一時的サービス](xref:fundamentals/dependency-injection#service-lifetimes)として登録されています。</span><span class="sxs-lookup"><span data-stu-id="16e2d-130">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="16e2d-131">利点:</span><span class="sxs-lookup"><span data-stu-id="16e2d-131">Benefits:</span></span>

* <span data-ttu-id="16e2d-132">クライアント要求ごとにアクティブ化 (スコープ サービスの挿入)</span><span class="sxs-lookup"><span data-stu-id="16e2d-132">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="16e2d-133">ミドルウェアの厳密な型指定</span><span class="sxs-lookup"><span data-stu-id="16e2d-133">Strong typing of middleware</span></span>

<span data-ttu-id="16e2d-134"><xref:Microsoft.AspNetCore.Http.IMiddleware> はクライアント要求 (接続) ごとにアクティブ化されているので、スコープ サービスをミドルウェアのコンストラクターに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-134"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="16e2d-135">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="16e2d-135">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="16e2d-136">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="16e2d-136">IMiddleware</span></span>

<span data-ttu-id="16e2d-137"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-137"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="16e2d-138">要求は、[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) メソッドによって処理され、ミドルウェアの実行を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-138">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="16e2d-139">規則でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="16e2d-139">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="16e2d-140"><xref:Microsoft.AspNetCore.Http.MiddlewareFactory> でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="16e2d-140">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="16e2d-141">ミドルウェア用に拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-141">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="16e2d-142"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> では、ファクトリでアクティブ化されたミドルウェアにオブジェクトを渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="16e2d-142">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="16e2d-143">ファクトリでアクティブ化されたミドルウェアは、`Startup.ConfigureServices` の組み込みのコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-143">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="16e2d-144">この 2 つのミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-144">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="16e2d-145">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="16e2d-145">IMiddlewareFactory</span></span>

<span data-ttu-id="16e2d-146"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-146"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="16e2d-147">ミドルウェア ファクトリの実装は、スコープ化されたサービスとして、コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="16e2d-147">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="16e2d-148">既定の <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> の実装である <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> は、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="16e2d-148">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="16e2d-149">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="16e2d-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
