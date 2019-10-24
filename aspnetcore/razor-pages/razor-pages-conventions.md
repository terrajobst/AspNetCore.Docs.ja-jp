---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: guardrex
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: a0a1eda69da34d1865fd11ef464c3697bcd01eff
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779219"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="96e54-103">ASP.NET Core での Razor ページのルートとアプリの規則</span><span class="sxs-lookup"><span data-stu-id="96e54-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="96e54-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="96e54-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="96e54-105">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="96e54-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="96e54-106">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="96e54-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="96e54-107">ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したりするには、ページの `@page` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="96e54-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="96e54-108">詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="96e54-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="96e54-109">ルートセグメントまたはパラメーター名として使用できない予約語があります。</span><span class="sxs-lookup"><span data-stu-id="96e54-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="96e54-110">詳細については、「[ルーティング: 予約済みルーティング名](xref:fundamentals/routing#reserved-routing-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="96e54-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="96e54-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="96e54-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="96e54-112">通信の種類</span><span class="sxs-lookup"><span data-stu-id="96e54-112">Scenario</span></span> | <span data-ttu-id="96e54-113">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="96e54-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="96e54-114">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="96e54-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="96e54-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="96e54-115">Conventions.Add</span></span><ul><li><span data-ttu-id="96e54-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="96e54-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="96e54-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="96e54-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="96e54-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="96e54-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="96e54-119">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="96e54-120">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="96e54-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="96e54-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="96e54-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="96e54-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="96e54-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="96e54-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="96e54-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="96e54-124">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="96e54-125">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="96e54-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="96e54-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="96e54-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="96e54-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="96e54-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="96e54-128">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="96e54-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="96e54-129">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="96e54-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="96e54-130">Razor Pages 規則は、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用して追加および構成され、`Startup` クラスのサービスコレクションで <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> します。</span><span class="sxs-lookup"><span data-stu-id="96e54-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="96e54-131">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-131">The following convention examples are explained later in this topic:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

::: moniker-end

## <a name="route-order"></a><span data-ttu-id="96e54-132">ルートの順序</span><span class="sxs-lookup"><span data-stu-id="96e54-132">Route order</span></span>

<span data-ttu-id="96e54-133">ルートは、処理 (ルート一致) の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="96e54-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="96e54-134">注文</span><span class="sxs-lookup"><span data-stu-id="96e54-134">Order</span></span>            | <span data-ttu-id="96e54-135">動作</span><span class="sxs-lookup"><span data-stu-id="96e54-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="96e54-136">-1</span><span class="sxs-lookup"><span data-stu-id="96e54-136">-1</span></span>               | <span data-ttu-id="96e54-137">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="96e54-138">0</span><span class="sxs-lookup"><span data-stu-id="96e54-138">0</span></span>                | <span data-ttu-id="96e54-139">順序が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="96e54-139">Order isn't specified (default value).</span></span> <span data-ttu-id="96e54-140">`Order` (`Order = null`) が割り当てられていない場合、ルートは処理のために 0 (ゼロ) に `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="96e54-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="96e54-141">1、2、&hellip; n</span><span class="sxs-lookup"><span data-stu-id="96e54-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="96e54-142">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="96e54-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="96e54-143">ルート処理は規約によって確立されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="96e54-144">ルートは順番に処理されます (-1、0、1、2、&hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="96e54-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="96e54-145">ルートの `Order`が同じである場合は、最も具体的なルートが最初に照合され、その後に特定のルートがより少なくなります。</span><span class="sxs-lookup"><span data-stu-id="96e54-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="96e54-146">同じ `Order` で同じ数のパラメーターが要求 URL と一致する場合、ルートは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="96e54-147">可能な場合は、確立されたルート処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="96e54-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="96e54-148">通常、ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="96e54-149">要求を正しくルーティングするようにルート `Order` のプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱し、保守が脆弱になることがあります。</span><span class="sxs-lookup"><span data-stu-id="96e54-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="96e54-150">アプリのルーティングスキームを簡略化するためにシークします。</span><span class="sxs-lookup"><span data-stu-id="96e54-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="96e54-151">このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="96e54-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="96e54-152">ただし、運用環境のアプリでルート `Order` を設定する方法を避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="96e54-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="96e54-153">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="96e54-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="96e54-154">MVC のトピックに記載されている情報については、 [「コントローラーアクションへのルーティング: 属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="96e54-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="96e54-155">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="96e54-155">Model conventions</span></span>

<span data-ttu-id="96e54-156">Razor Pages に適用されるモデルの[規則](xref:mvc/controllers/application-model#conventions)を追加するには、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> のデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="96e54-157">ルートモデルの規則をすべてのページに追加する</span><span class="sxs-lookup"><span data-stu-id="96e54-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="96e54-158"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページルートモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="96e54-159">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="96e54-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="96e54-161">これにより、サンプルアプリで次のルート一致動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="96e54-162">`TheContactPage/{text?}` のルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="96e54-163">連絡先ページのルートには `null` (`Order = 0`) の既定の順序があるため、`{globalTemplate?}` ルートテンプレートの前に一致します。</span><span class="sxs-lookup"><span data-stu-id="96e54-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="96e54-164">`{aboutTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="96e54-165">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="96e54-166">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="96e54-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="96e54-167">`{otherPagesTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="96e54-168">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="96e54-169">*Pages/otherpages*フォルダー内のいずれかのページがルートパラメーター (`/OtherPages/Page1/RouteDataValue`など) で要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) は読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="96e54-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="96e54-170">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="96e54-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="96e54-171">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="96e54-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="96e54-172">MVC が `Startup.ConfigureServices`のサービスコレクションに追加されると、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の追加などの Razor Pages オプションが追加されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="96e54-173">例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="96e54-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="96e54-174">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="96e54-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="96e54-177">すべてのページにアプリモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="96e54-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="96e54-178"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページアプリモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="96e54-179">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="96e54-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="96e54-180">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="96e54-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="96e54-181">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="96e54-182">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="96e54-183">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="96e54-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="96e54-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="96e54-185">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="96e54-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="96e54-187">すべてのページにハンドラーモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="96e54-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="96e54-188"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページハンドラーモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="96e54-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="96e54-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="96e54-190">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="96e54-190">Page route action conventions</span></span>

<span data-ttu-id="96e54-191"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="96e54-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="96e54-192">フォルダールートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="96e54-192">Folder route model convention</span></span>

<span data-ttu-id="96e54-193"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページについて <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="96e54-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="96e54-194">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="96e54-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="96e54-196">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="96e54-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="96e54-197">*Pages/otherpages*フォルダー内のページがルートパラメーター値 (`/OtherPages/Page1/RouteDataValue`など) で要求されている場合、"RouteDataValue" は、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="96e54-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="96e54-198">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="96e54-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="96e54-199">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="96e54-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="96e54-200">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="96e54-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="96e54-203">ページルートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="96e54-203">Page route model convention</span></span>

<span data-ttu-id="96e54-204"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="96e54-205">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="96e54-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="96e54-207">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="96e54-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="96e54-208">`/About/RouteDataValue`でルートパラメーター値を使用して About ページが要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["aboutTemplate"]` (`Order`) ではなくなります。</span><span class="sxs-lookup"><span data-stu-id="96e54-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="96e54-209">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="96e54-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="96e54-210">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="96e54-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="96e54-211">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="96e54-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="96e54-214">パラメータートランスフォーマーを使用してページルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="96e54-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="96e54-215">ASP.NET Core によって生成されたページルートは、パラメータートランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="96e54-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="96e54-216">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="96e54-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="96e54-217">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="96e54-218">`PageRouteTransformerConvention` ページルートモデル規則では、アプリで自動的に生成されたページルートのフォルダーとファイル名のセグメントにパラメータートランスフォーマーを適用します。</span><span class="sxs-lookup"><span data-stu-id="96e54-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="96e54-219">たとえば、 *//* //の Razor Pages ファイルでは、ルートが `/SubscriptionManagement/ViewAll` から `/subscription-management/view-all`に書き直されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="96e54-220">`PageRouteTransformerConvention` は、Razor Pages フォルダーとファイル名から取得された、自動的に生成されたページルートのセグメントのみを変換します。</span><span class="sxs-lookup"><span data-stu-id="96e54-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="96e54-221">`@page` ディレクティブを使用して追加されたルートセグメントは変換されません。</span><span class="sxs-lookup"><span data-stu-id="96e54-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="96e54-222">この規則では、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>によって追加されたルートを変換することもできません。</span><span class="sxs-lookup"><span data-stu-id="96e54-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="96e54-223">`PageRouteTransformerConvention` は `Startup.ConfigureServices`のオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

## <a name="configure-a-page-route"></a><span data-ttu-id="96e54-224">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="96e54-224">Configure a page route</span></span>

<span data-ttu-id="96e54-225">指定したページパスにあるページへのルートを構成するには、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="96e54-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="96e54-226">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="96e54-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="96e54-227">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="96e54-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="96e54-228">サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="96e54-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="96e54-229">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="96e54-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="96e54-230">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="96e54-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="96e54-231">また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="96e54-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="96e54-232">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="96e54-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="96e54-235">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="96e54-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="96e54-236">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="96e54-239">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="96e54-239">Page model action conventions</span></span>

<span data-ttu-id="96e54-240"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページモデルプロバイダーは、ページモデルを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="96e54-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="96e54-241">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="96e54-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="96e54-242">このセクションの例では、サンプルアプリで <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>である `AddHeaderAttribute` クラスを使用して、応答ヘッダーを適用します。</span><span class="sxs-lookup"><span data-stu-id="96e54-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="96e54-243">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="96e54-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="96e54-244">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="96e54-244">**Folder app model convention**</span></span>

<span data-ttu-id="96e54-245"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスでアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="96e54-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="96e54-246">このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="96e54-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="96e54-247">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="96e54-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="96e54-249">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="96e54-249">**Page app model convention**</span></span>

<span data-ttu-id="96e54-250"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="96e54-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="96e54-251">サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="96e54-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="96e54-252">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="96e54-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="96e54-254">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="96e54-254">**Configure a filter**</span></span>

<span data-ttu-id="96e54-255">指定したフィルターを適用するように <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成します。</span><span class="sxs-lookup"><span data-stu-id="96e54-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="96e54-256">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="96e54-257">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="96e54-258">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="96e54-259">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="96e54-260">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="96e54-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="96e54-261">アクションフィルターは Razor Pages によって無視されるため、パスに `OtherPages/Page2`が含まれていない場合、`EmptyFilter` は意図したとおりには効果がありません。</span><span class="sxs-lookup"><span data-stu-id="96e54-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="96e54-262">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="96e54-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="96e54-264">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="96e54-264">**Configure a filter factory**</span></span>

<span data-ttu-id="96e54-265">指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> ます。</span><span class="sxs-lookup"><span data-stu-id="96e54-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="96e54-266">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="96e54-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="96e54-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="96e54-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="96e54-268">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="96e54-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="96e54-270">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="96e54-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="96e54-271">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="96e54-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="96e54-272">その他の型の MVC フィルターは、[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)を使用するために利用できます。</span><span class="sxs-lookup"><span data-stu-id="96e54-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="96e54-273">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="96e54-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="96e54-274">ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。</span><span class="sxs-lookup"><span data-stu-id="96e54-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="96e54-275">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="96e54-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96e54-276">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="96e54-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
