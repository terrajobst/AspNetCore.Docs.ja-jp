---
title: ASP.NET Core の Razor ページのフィルター メソッド
author: Rick-Anderson
description: ASP.NET Core の Razor ページのフィルター メソッドを作成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: a60b17685c6f836de7c0afcc5b89a9894fb8b28f
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447232"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="4260a-103">ASP.NET Core の Razor ページのフィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="4260a-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4260a-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4260a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4260a-105">Razor ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor ページでコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="4260a-106">Razor ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。</span><span class="sxs-lookup"><span data-stu-id="4260a-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="4260a-107">Razor ページ フィルターとは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4260a-107">Razor Page filters:</span></span>

* <span data-ttu-id="4260a-108">モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="4260a-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="4260a-109">モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="4260a-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="4260a-110">ハンドラー メソッドの実行後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="4260a-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="4260a-111">ページまたはグローバルに実装できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="4260a-112">特定のページ ハンドラー メソッドには適用できません。</span><span class="sxs-lookup"><span data-stu-id="4260a-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="4260a-113">[依存関係の挿入](xref:fundamentals/dependency-injection)(DI) によってコンストラクターの依存関係を設定できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="4260a-114">詳細については、「 [Servicefilterattribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) 」と「 [typefilterattribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4260a-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="4260a-115">ページコンストラクターとミドルウェアは、ハンドラーメソッドの実行前にカスタムコードの実行を有効にしますが、Razor ページフィルターのみを使用して <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> およびページにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="4260a-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="4260a-116">ミドルウェアは `HttpContext`にアクセスできますが、"ページコンテキスト" にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="4260a-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="4260a-117">フィルターには <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> 派生パラメーターがあり、`HttpContext`へのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="4260a-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="4260a-118">たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。</span><span class="sxs-lookup"><span data-stu-id="4260a-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="4260a-119">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4260a-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4260a-120">Razor ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="4260a-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="4260a-121">同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="4260a-121">Synchronous methods:</span></span>

  * <span data-ttu-id="4260a-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="4260a-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドの実行前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="4260a-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : アクション結果の前の、ハンドラー メソッドの実行前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="4260a-125">非同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="4260a-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="4260a-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="4260a-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドが呼び出される前に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="4260a-128">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="4260a-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="4260a-129">フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4260a-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4260a-130">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4260a-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4260a-131">両方のインターフェイスが実装されている場合は、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="4260a-132">ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。</span><span class="sxs-lookup"><span data-stu-id="4260a-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="4260a-133">Razor ページにフィルターをグローバルに実装する</span><span class="sxs-lookup"><span data-stu-id="4260a-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="4260a-134">`IAsyncPageFilter` は、次のコードによって実装されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="4260a-135">上記のコードでは、`ProcessUserAgent.Write` ユーザーが指定したコードがユーザーエージェント文字列で動作します。</span><span class="sxs-lookup"><span data-stu-id="4260a-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="4260a-136">次のコードは、`SampleAsyncPageFilter` クラスで `Startup` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4260a-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="4260a-137">次のコードは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を呼び出して、`SampleAsyncPageFilter` を */ムービー*内のページのみに適用します。</span><span class="sxs-lookup"><span data-stu-id="4260a-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="4260a-138">次のコードは、同期 `IPageFilter` を実装します。</span><span class="sxs-lookup"><span data-stu-id="4260a-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="4260a-139">次のコードは、`SamplePageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4260a-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="4260a-140">フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する</span><span class="sxs-lookup"><span data-stu-id="4260a-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="4260a-141">次のコードは、非同期の Razor ページフィルターをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4260a-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="4260a-142">フィルター属性を実装する</span><span class="sxs-lookup"><span data-stu-id="4260a-142">Implement a filter attribute</span></span>

<span data-ttu-id="4260a-143">組み込みの属性ベースのフィルター <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> フィルターをサブクラス化できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="4260a-144">次のフィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4260a-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="4260a-145">次のコードは、`AddHeader` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="4260a-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="4260a-146">ブラウザー開発者ツールなどのツールを使用して、ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="4260a-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="4260a-147">**[応答ヘッダー]** の下に `author: Rick` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="4260a-148">順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4260a-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="4260a-149">フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4260a-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="4260a-150">Authorize フィルター属性</span><span class="sxs-lookup"><span data-stu-id="4260a-150">Authorize filter attribute</span></span>

<span data-ttu-id="4260a-151">[ に ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0)Authorize`PageModel` 属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4260a-152">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4260a-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4260a-153">Razor ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor ページでコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="4260a-154">Razor ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。</span><span class="sxs-lookup"><span data-stu-id="4260a-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="4260a-155">Razor ページ フィルターとは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4260a-155">Razor Page filters:</span></span>

* <span data-ttu-id="4260a-156">モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="4260a-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="4260a-157">モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="4260a-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="4260a-158">ハンドラー メソッドの実行後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="4260a-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="4260a-159">ページまたはグローバルに実装できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="4260a-160">特定のページ ハンドラー メソッドには適用できません。</span><span class="sxs-lookup"><span data-stu-id="4260a-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="4260a-161">コードは、ページ コンストラクターまたはミドルウェアを使用してハンドラー メソッドの実行前に実行できますが、[HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext) にアクセスできるのは Razor ページ フィルターのみです。</span><span class="sxs-lookup"><span data-stu-id="4260a-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="4260a-162">フィルターには、[ へのアクセスを提供する ](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0)FilterContext`HttpContext` 派生のパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="4260a-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="4260a-163">たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。</span><span class="sxs-lookup"><span data-stu-id="4260a-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="4260a-164">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4260a-164">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4260a-165">Razor ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="4260a-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="4260a-166">同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="4260a-166">Synchronous methods:</span></span>

  * <span data-ttu-id="4260a-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="4260a-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドの実行前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="4260a-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : アクション結果の前の、ハンドラー メソッドの実行前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="4260a-170">非同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="4260a-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="4260a-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="4260a-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドが呼び出される前に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="4260a-173">フィルター インターフェイスの同期と非同期バージョンの両方ではなく、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="4260a-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="4260a-174">フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4260a-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4260a-175">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4260a-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4260a-176">両方のインターフェイスが実装されている場合は、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="4260a-177">ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。</span><span class="sxs-lookup"><span data-stu-id="4260a-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="4260a-178">Razor ページにフィルターをグローバルに実装する</span><span class="sxs-lookup"><span data-stu-id="4260a-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="4260a-179">`IAsyncPageFilter` は、次のコードによって実装されます。</span><span class="sxs-lookup"><span data-stu-id="4260a-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="4260a-180">前述のコードでは、[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) は不要です。</span><span class="sxs-lookup"><span data-stu-id="4260a-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="4260a-181">これは、アプリケーションのトレース情報を提供するためにサンプルで使用されています。</span><span class="sxs-lookup"><span data-stu-id="4260a-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="4260a-182">次のコードは、`SampleAsyncPageFilter` クラスで `Startup` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4260a-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="4260a-183">次は、完全な `Startup` クラスのコードです。</span><span class="sxs-lookup"><span data-stu-id="4260a-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="4260a-184">次のコードは、`AddFolderApplicationModelConvention` を呼び出し、`SampleAsyncPageFilter`/subFolder*のページにのみ* を適用します。</span><span class="sxs-lookup"><span data-stu-id="4260a-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="4260a-185">次のコードは、同期 `IPageFilter` を実装します。</span><span class="sxs-lookup"><span data-stu-id="4260a-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="4260a-186">次のコードは、`SamplePageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4260a-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="4260a-187">フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する</span><span class="sxs-lookup"><span data-stu-id="4260a-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="4260a-188">次のコードは、同期 Razor ページ フィルターをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4260a-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="4260a-189">フィルター属性を実装する</span><span class="sxs-lookup"><span data-stu-id="4260a-189">Implement a filter attribute</span></span>

<span data-ttu-id="4260a-190">組み込みの属性ベースのフィルターである [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) フィルターはサブクラス化することができます。</span><span class="sxs-lookup"><span data-stu-id="4260a-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="4260a-191">次のフィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4260a-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="4260a-192">次のコードは、`AddHeader` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="4260a-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="4260a-193">順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4260a-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="4260a-194">フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4260a-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="4260a-195">Authorize フィルター属性</span><span class="sxs-lookup"><span data-stu-id="4260a-195">Authorize filter attribute</span></span>

<span data-ttu-id="4260a-196">[ に ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0)Authorize`PageModel` 属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="4260a-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
