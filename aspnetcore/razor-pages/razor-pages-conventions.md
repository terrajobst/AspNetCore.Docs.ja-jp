---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: guardrex
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: d8377c0a0b8a29fe4b6a7fa67beeff84927c8b74
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114771"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="22e29-103">ASP.NET Core での Razor ページのルートとアプリの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="22e29-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="22e29-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22e29-105">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="22e29-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="22e29-106">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="22e29-107">ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したりするには、ページの `@page` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="22e29-108">詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="22e29-109">ルートセグメントまたはパラメーター名として使用できない予約語があります。</span><span class="sxs-lookup"><span data-stu-id="22e29-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="22e29-110">詳細については、「[ルーティング: 予約済みルーティング名](xref:fundamentals/routing#reserved-routing-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="22e29-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="22e29-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="22e29-112">シナリオ</span><span class="sxs-lookup"><span data-stu-id="22e29-112">Scenario</span></span> | <span data-ttu-id="22e29-113">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="22e29-114">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="22e29-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="22e29-115">Conventions.Add</span></span><ul><li><span data-ttu-id="22e29-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="22e29-119">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="22e29-120">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="22e29-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="22e29-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="22e29-124">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="22e29-125">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="22e29-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-128">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="22e29-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="22e29-129">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="22e29-130">Razor Pages 規則は、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用して追加および構成され、`Startup` クラスのサービスコレクションで <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> します。</span><span class="sxs-lookup"><span data-stu-id="22e29-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="22e29-131">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-131">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="22e29-132">ルートの順序</span><span class="sxs-lookup"><span data-stu-id="22e29-132">Route order</span></span>

<span data-ttu-id="22e29-133">ルートは、処理 (ルート一致) の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="22e29-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="22e29-134">Order</span><span class="sxs-lookup"><span data-stu-id="22e29-134">Order</span></span>            | <span data-ttu-id="22e29-135">動作</span><span class="sxs-lookup"><span data-stu-id="22e29-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="22e29-136">-1</span><span class="sxs-lookup"><span data-stu-id="22e29-136">-1</span></span>               | <span data-ttu-id="22e29-137">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="22e29-138">0</span><span class="sxs-lookup"><span data-stu-id="22e29-138">0</span></span>                | <span data-ttu-id="22e29-139">順序が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="22e29-139">Order isn't specified (default value).</span></span> <span data-ttu-id="22e29-140">`Order` (`Order = null`) が割り当てられていない場合、ルートは処理のために 0 (ゼロ) に `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="22e29-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="22e29-141">1、2、&hellip; n</span><span class="sxs-lookup"><span data-stu-id="22e29-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="22e29-142">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="22e29-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="22e29-143">ルート処理は規約によって確立されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="22e29-144">ルートは順番に処理されます (-1、0、1、2、&hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="22e29-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="22e29-145">ルートの `Order`が同じである場合は、最も具体的なルートが最初に照合され、その後に特定のルートがより少なくなります。</span><span class="sxs-lookup"><span data-stu-id="22e29-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="22e29-146">同じ `Order` で同じ数のパラメーターが要求 URL と一致する場合、ルートは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="22e29-147">可能な場合は、確立されたルート処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="22e29-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="22e29-148">通常、ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="22e29-149">要求を正しくルーティングするようにルート `Order` のプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱し、保守が脆弱になることがあります。</span><span class="sxs-lookup"><span data-stu-id="22e29-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="22e29-150">アプリのルーティングスキームを簡略化するためにシークします。</span><span class="sxs-lookup"><span data-stu-id="22e29-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="22e29-151">このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="22e29-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="22e29-152">ただし、運用環境のアプリでルート `Order` を設定する方法を避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="22e29-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="22e29-153">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="22e29-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="22e29-154">MVC のトピックに記載されている情報については、 [「コントローラーアクションへのルーティング: 属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="22e29-155">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-155">Model conventions</span></span>

<span data-ttu-id="22e29-156">Razor Pages に適用されるモデルの[規則](xref:mvc/controllers/application-model#conventions)を追加するには、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> のデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="22e29-157">ルートモデルの規則をすべてのページに追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="22e29-158"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページルートモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="22e29-159">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="22e29-161">これにより、サンプルアプリで次のルート一致動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="22e29-162">`TheContactPage/{text?}` のルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="22e29-163">連絡先ページのルートには `null` (`Order = 0`) の既定の順序があるため、`{globalTemplate?}` ルートテンプレートの前に一致します。</span><span class="sxs-lookup"><span data-stu-id="22e29-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="22e29-164">`{aboutTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="22e29-165">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="22e29-166">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`RouteData.Values["aboutTemplate"]` プロパティが設定されるため `Order = 2` (`Order`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="22e29-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="22e29-167">`{otherPagesTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="22e29-168">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="22e29-169">*Pages/otherpages*フォルダー内のいずれかのページがルートパラメーター (`/OtherPages/Page1/RouteDataValue`など) で要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) は読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="22e29-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-170">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-171">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-172">MVC が `Startup.ConfigureServices`のサービスコレクションに追加されると、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の追加などの Razor Pages オプションが追加されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="22e29-173">例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="22e29-174">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="22e29-177">すべてのページにアプリモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="22e29-178"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページアプリモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="22e29-179">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="22e29-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="22e29-180">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="22e29-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="22e29-181">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="22e29-182">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="22e29-183">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="22e29-185">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="22e29-187">すべてのページにハンドラーモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="22e29-188"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページハンドラーモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="22e29-190">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-190">Page route action conventions</span></span>

<span data-ttu-id="22e29-191"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="22e29-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="22e29-192">フォルダールートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-192">Folder route model convention</span></span>

<span data-ttu-id="22e29-193"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページについて <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="22e29-194">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="22e29-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="22e29-196">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="22e29-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="22e29-197">*Pages/otherpages*フォルダー内のページがルートパラメーター値 (`/OtherPages/Page1/RouteDataValue`など) で要求されている場合、"RouteDataValue" は、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="22e29-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-198">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-199">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-200">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="22e29-203">ページルートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-203">Page route model convention</span></span>

<span data-ttu-id="22e29-204"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="22e29-205">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="22e29-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="22e29-207">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="22e29-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="22e29-208">`/About/RouteDataValue`でルートパラメーター値を使用して About ページが要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["aboutTemplate"]` (`Order`) ではなくなります。</span><span class="sxs-lookup"><span data-stu-id="22e29-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-209">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-210">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-211">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="22e29-214">パラメータートランスフォーマーを使用してページルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="22e29-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="22e29-215">ASP.NET Core によって生成されたページルートは、パラメータートランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="22e29-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="22e29-216">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="22e29-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="22e29-217">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="22e29-218">`PageRouteTransformerConvention` ページルートモデル規則では、アプリで自動的に生成されたページルートのフォルダーとファイル名のセグメントにパラメータートランスフォーマーを適用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="22e29-219">たとえば、 *//* //の Razor Pages ファイルでは、ルートが `/SubscriptionManagement/ViewAll` から `/subscription-management/view-all`に書き直されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="22e29-220">`PageRouteTransformerConvention` は、Razor Pages フォルダーとファイル名から取得された、自動的に生成されたページルートのセグメントのみを変換します。</span><span class="sxs-lookup"><span data-stu-id="22e29-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="22e29-221">`@page` ディレクティブを使用して追加されたルートセグメントは変換されません。</span><span class="sxs-lookup"><span data-stu-id="22e29-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="22e29-222">この規則では、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>によって追加されたルートを変換することもできません。</span><span class="sxs-lookup"><span data-stu-id="22e29-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="22e29-223">`PageRouteTransformerConvention` は `Startup.ConfigureServices`のオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="22e29-224">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="22e29-224">Configure a page route</span></span>

<span data-ttu-id="22e29-225">指定したページパスにあるページへのルートを構成するには、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="22e29-226">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="22e29-227">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="22e29-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="22e29-228">サンプル アプリでは、`/TheContactPage`Contact.cshtml*の* へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="22e29-229">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="22e29-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="22e29-230">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="22e29-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="22e29-231">また、訪問者が `@page` ルートでページにアクセスする場合、ページの `/Contact` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="22e29-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="22e29-232">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="22e29-235">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="22e29-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="22e29-236">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="22e29-239">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-239">Page model action conventions</span></span>

<span data-ttu-id="22e29-240"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページモデルプロバイダーは、ページモデルを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="22e29-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="22e29-241">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="22e29-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="22e29-242">このセクションの例では、サンプルアプリで <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>である `AddHeaderAttribute` クラスを使用して、応答ヘッダーを適用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="22e29-243">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="22e29-244">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="22e29-244">**Folder app model convention**</span></span>

<span data-ttu-id="22e29-245"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスでアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="22e29-246">このサンプルでは、ヘッダー (`AddFolderApplicationModelConvention`) をアプリの `OtherPagesHeader`OtherPages *フォルダー内にあるページに追加して、* を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="22e29-247">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="22e29-249">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="22e29-249">**Page app model convention**</span></span>

<span data-ttu-id="22e29-250"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="22e29-251">サンプルでは、ヘッダー (`AddPageApplicationModelConvention`) を [About] ページに追加して、`AboutHeader` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="22e29-252">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="22e29-254">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="22e29-254">**Configure a filter**</span></span>

<span data-ttu-id="22e29-255">指定したフィルターを適用するように <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="22e29-256">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="22e29-257">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="22e29-258">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="22e29-259">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="22e29-260">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="22e29-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="22e29-261">アクションフィルターは Razor Pages によって無視されるため、パスに `OtherPages/Page2`が含まれていない場合、`EmptyFilter` は意図したとおりには効果がありません。</span><span class="sxs-lookup"><span data-stu-id="22e29-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="22e29-262">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="22e29-264">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="22e29-264">**Configure a filter factory**</span></span>

<span data-ttu-id="22e29-265">指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> ます。</span><span class="sxs-lookup"><span data-stu-id="22e29-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="22e29-266">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー ([) を追加して、](xref:mvc/controllers/filters#ifilterfactory)フィルター ファクトリ`FilterFactoryHeader`を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="22e29-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="22e29-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="22e29-268">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="22e29-270">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="22e29-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="22e29-271">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="22e29-272">その他の型の MVC フィルターは、[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)を使用するために利用できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="22e29-273">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="22e29-274">ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。</span><span class="sxs-lookup"><span data-stu-id="22e29-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="22e29-275">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22e29-276">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="22e29-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="22e29-277">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="22e29-277">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="22e29-278">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-278">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="22e29-279">ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したりするには、ページの `@page` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-279">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="22e29-280">詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-280">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="22e29-281">ルートセグメントまたはパラメーター名として使用できない予約語があります。</span><span class="sxs-lookup"><span data-stu-id="22e29-281">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="22e29-282">詳細については、「[ルーティング: 予約済みルーティング名](xref:fundamentals/routing#reserved-routing-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-282">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="22e29-283">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="22e29-283">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="22e29-284">シナリオ</span><span class="sxs-lookup"><span data-stu-id="22e29-284">Scenario</span></span> | <span data-ttu-id="22e29-285">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-285">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="22e29-286">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-286">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="22e29-287">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="22e29-287">Conventions.Add</span></span><ul><li><span data-ttu-id="22e29-288">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-288">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-289">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-289">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-290">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-290">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="22e29-291">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-291">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="22e29-292">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-292">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="22e29-293">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-293">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-294">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-294">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-295">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="22e29-295">AddPageRoute</span></span></li></ul> | <span data-ttu-id="22e29-296">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-296">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="22e29-297">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-297">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="22e29-298">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-298">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-299">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-299">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-300">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="22e29-300">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="22e29-301">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-301">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="22e29-302">Razor Pages 規則は、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用して追加および構成され、`Startup` クラスのサービスコレクションで <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> します。</span><span class="sxs-lookup"><span data-stu-id="22e29-302">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="22e29-303">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-303">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="22e29-304">ルートの順序</span><span class="sxs-lookup"><span data-stu-id="22e29-304">Route order</span></span>

<span data-ttu-id="22e29-305">ルートは、処理 (ルート一致) の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="22e29-305">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="22e29-306">Order</span><span class="sxs-lookup"><span data-stu-id="22e29-306">Order</span></span>            | <span data-ttu-id="22e29-307">動作</span><span class="sxs-lookup"><span data-stu-id="22e29-307">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="22e29-308">-1</span><span class="sxs-lookup"><span data-stu-id="22e29-308">-1</span></span>               | <span data-ttu-id="22e29-309">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-309">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="22e29-310">0</span><span class="sxs-lookup"><span data-stu-id="22e29-310">0</span></span>                | <span data-ttu-id="22e29-311">順序が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="22e29-311">Order isn't specified (default value).</span></span> <span data-ttu-id="22e29-312">`Order` (`Order = null`) が割り当てられていない場合、ルートは処理のために 0 (ゼロ) に `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="22e29-312">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="22e29-313">1、2、&hellip; n</span><span class="sxs-lookup"><span data-stu-id="22e29-313">1, 2, &hellip; n</span></span> | <span data-ttu-id="22e29-314">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="22e29-314">Specifies the route processing order.</span></span> |

<span data-ttu-id="22e29-315">ルート処理は規約によって確立されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-315">Route processing is established by convention:</span></span>

* <span data-ttu-id="22e29-316">ルートは順番に処理されます (-1、0、1、2、&hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="22e29-316">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="22e29-317">ルートの `Order`が同じである場合は、最も具体的なルートが最初に照合され、その後に特定のルートがより少なくなります。</span><span class="sxs-lookup"><span data-stu-id="22e29-317">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="22e29-318">同じ `Order` で同じ数のパラメーターが要求 URL と一致する場合、ルートは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-318">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="22e29-319">可能な場合は、確立されたルート処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="22e29-319">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="22e29-320">通常、ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-320">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="22e29-321">要求を正しくルーティングするようにルート `Order` のプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱し、保守が脆弱になることがあります。</span><span class="sxs-lookup"><span data-stu-id="22e29-321">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="22e29-322">アプリのルーティングスキームを簡略化するためにシークします。</span><span class="sxs-lookup"><span data-stu-id="22e29-322">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="22e29-323">このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="22e29-323">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="22e29-324">ただし、運用環境のアプリでルート `Order` を設定する方法を避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="22e29-324">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="22e29-325">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="22e29-325">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="22e29-326">MVC のトピックに記載されている情報については、 [「コントローラーアクションへのルーティング: 属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-326">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="22e29-327">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-327">Model conventions</span></span>

<span data-ttu-id="22e29-328">Razor Pages に適用されるモデルの[規則](xref:mvc/controllers/application-model#conventions)を追加するには、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> のデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-328">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="22e29-329">ルートモデルの規則をすべてのページに追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-329">Add a route model convention to all pages</span></span>

<span data-ttu-id="22e29-330"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページルートモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-330">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="22e29-331">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-331">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-332"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-332">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="22e29-333">これにより、サンプルアプリで次のルート一致動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-333">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="22e29-334">`TheContactPage/{text?}` のルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-334">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="22e29-335">連絡先ページのルートには `null` (`Order = 0`) の既定の順序があるため、`{globalTemplate?}` ルートテンプレートの前に一致します。</span><span class="sxs-lookup"><span data-stu-id="22e29-335">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="22e29-336">`{aboutTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-336">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="22e29-337">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-337">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="22e29-338">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`RouteData.Values["aboutTemplate"]` プロパティが設定されるため `Order = 2` (`Order`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="22e29-338">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="22e29-339">`{otherPagesTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-339">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="22e29-340">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-340">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="22e29-341">*Pages/otherpages*フォルダー内のいずれかのページがルートパラメーター (`/OtherPages/Page1/RouteDataValue`など) で要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) は読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="22e29-341">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-342">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-342">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-343">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-343">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-344">MVC が `Startup.ConfigureServices`のサービスコレクションに追加されると、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の追加などの Razor Pages オプションが追加されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-344">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="22e29-345">例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-345">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="22e29-346">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-346">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="22e29-349">すべてのページにアプリモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-349">Add an app model convention to all pages</span></span>

<span data-ttu-id="22e29-350"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページアプリモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-350">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="22e29-351">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="22e29-351">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="22e29-352">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="22e29-352">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="22e29-353">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-353">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="22e29-354">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-354">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="22e29-355">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-355">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-356">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-356">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="22e29-357">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-357">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="22e29-359">すべてのページにハンドラーモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-359">Add a handler model convention to all pages</span></span>

<span data-ttu-id="22e29-360"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページハンドラーモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-360">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-361">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-361">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="22e29-362">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-362">Page route action conventions</span></span>

<span data-ttu-id="22e29-363"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="22e29-363">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="22e29-364">フォルダールートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-364">Folder route model convention</span></span>

<span data-ttu-id="22e29-365"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページについて <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-365">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="22e29-366">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-366">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="22e29-367"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-367">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="22e29-368">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="22e29-368">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="22e29-369">*Pages/otherpages*フォルダー内のページがルートパラメーター値 (`/OtherPages/Page1/RouteDataValue`など) で要求されている場合、"RouteDataValue" は、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="22e29-369">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-370">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-370">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-371">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-371">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-372">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-372">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="22e29-375">ページルートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-375">Page route model convention</span></span>

<span data-ttu-id="22e29-376"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-376">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="22e29-377">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-377">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="22e29-378"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-378">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="22e29-379">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="22e29-379">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="22e29-380">`/About/RouteDataValue`でルートパラメーター値を使用して About ページが要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["aboutTemplate"]` (`Order`) ではなくなります。</span><span class="sxs-lookup"><span data-stu-id="22e29-380">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-381">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-381">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-382">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-382">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-383">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-383">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="22e29-386">パラメータートランスフォーマーを使用してページルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="22e29-386">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="22e29-387">ASP.NET Core によって生成されたページルートは、パラメータートランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="22e29-387">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="22e29-388">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="22e29-388">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="22e29-389">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-389">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="22e29-390">`PageRouteTransformerConvention` ページルートモデル規則では、アプリで自動的に生成されたページルートのフォルダーとファイル名のセグメントにパラメータートランスフォーマーを適用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-390">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="22e29-391">たとえば、 *//* //の Razor Pages ファイルでは、ルートが `/SubscriptionManagement/ViewAll` から `/subscription-management/view-all`に書き直されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-391">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="22e29-392">`PageRouteTransformerConvention` は、Razor Pages フォルダーとファイル名から取得された、自動的に生成されたページルートのセグメントのみを変換します。</span><span class="sxs-lookup"><span data-stu-id="22e29-392">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="22e29-393">`@page` ディレクティブを使用して追加されたルートセグメントは変換されません。</span><span class="sxs-lookup"><span data-stu-id="22e29-393">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="22e29-394">この規則では、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>によって追加されたルートを変換することもできません。</span><span class="sxs-lookup"><span data-stu-id="22e29-394">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="22e29-395">`PageRouteTransformerConvention` は `Startup.ConfigureServices`のオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-395">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="22e29-396">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="22e29-396">Configure a page route</span></span>

<span data-ttu-id="22e29-397">指定したページパスにあるページへのルートを構成するには、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-397">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="22e29-398">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-398">Generated links to the page use your specified route.</span></span> <span data-ttu-id="22e29-399">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="22e29-399">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="22e29-400">サンプル アプリでは、`/TheContactPage`Contact.cshtml*の* へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-400">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="22e29-401">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="22e29-401">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="22e29-402">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="22e29-402">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="22e29-403">また、訪問者が `@page` ルートでページにアクセスする場合、ページの `/Contact` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="22e29-403">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="22e29-404">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-404">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="22e29-407">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="22e29-407">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="22e29-408">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-408">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="22e29-411">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-411">Page model action conventions</span></span>

<span data-ttu-id="22e29-412"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページモデルプロバイダーは、ページモデルを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="22e29-412">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="22e29-413">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="22e29-413">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="22e29-414">このセクションの例では、サンプルアプリで <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>である `AddHeaderAttribute` クラスを使用して、応答ヘッダーを適用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-414">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="22e29-415">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-415">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="22e29-416">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="22e29-416">**Folder app model convention**</span></span>

<span data-ttu-id="22e29-417"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスでアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-417">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="22e29-418">このサンプルでは、ヘッダー (`AddFolderApplicationModelConvention`) をアプリの `OtherPagesHeader`OtherPages *フォルダー内にあるページに追加して、* を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-418">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="22e29-419">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-419">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="22e29-421">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="22e29-421">**Page app model convention**</span></span>

<span data-ttu-id="22e29-422"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-422">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="22e29-423">サンプルでは、ヘッダー (`AddPageApplicationModelConvention`) を [About] ページに追加して、`AboutHeader` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-423">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="22e29-424">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-424">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="22e29-426">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="22e29-426">**Configure a filter**</span></span>

<span data-ttu-id="22e29-427">指定したフィルターを適用するように <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-427"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="22e29-428">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-428">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="22e29-429">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-429">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="22e29-430">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-430">If the condition passes, a header is added.</span></span> <span data-ttu-id="22e29-431">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-431">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="22e29-432">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="22e29-432">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="22e29-433">アクションフィルターは Razor Pages によって無視されるため、パスに `OtherPages/Page2`が含まれていない場合、`EmptyFilter` は意図したとおりには効果がありません。</span><span class="sxs-lookup"><span data-stu-id="22e29-433">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="22e29-434">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-434">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="22e29-436">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="22e29-436">**Configure a filter factory**</span></span>

<span data-ttu-id="22e29-437">指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> ます。</span><span class="sxs-lookup"><span data-stu-id="22e29-437"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="22e29-438">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー ([) を追加して、](xref:mvc/controllers/filters#ifilterfactory)フィルター ファクトリ`FilterFactoryHeader`を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="22e29-438">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="22e29-439">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-439">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="22e29-440">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-440">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="22e29-442">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="22e29-442">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="22e29-443">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-443">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="22e29-444">その他の型の MVC フィルターは、[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)を使用するために利用できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-444">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="22e29-445">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-445">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="22e29-446">ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。</span><span class="sxs-lookup"><span data-stu-id="22e29-446">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="22e29-447">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-447">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22e29-448">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="22e29-448">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="22e29-449">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="22e29-449">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="22e29-450">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-450">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="22e29-451">ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したりするには、ページの `@page` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-451">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="22e29-452">詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-452">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="22e29-453">ルートセグメントまたはパラメーター名として使用できない予約語があります。</span><span class="sxs-lookup"><span data-stu-id="22e29-453">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="22e29-454">詳細については、「[ルーティング: 予約済みルーティング名](xref:fundamentals/routing#reserved-routing-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-454">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="22e29-455">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="22e29-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="22e29-456">シナリオ</span><span class="sxs-lookup"><span data-stu-id="22e29-456">Scenario</span></span> | <span data-ttu-id="22e29-457">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-457">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="22e29-458">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-458">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="22e29-459">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="22e29-459">Conventions.Add</span></span><ul><li><span data-ttu-id="22e29-460">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-460">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-461">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-461">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-462">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-462">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="22e29-463">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-463">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="22e29-464">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-464">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="22e29-465">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-465">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-466">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-466">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="22e29-467">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="22e29-467">AddPageRoute</span></span></li></ul> | <span data-ttu-id="22e29-468">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-468">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="22e29-469">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-469">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="22e29-470">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-470">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-471">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="22e29-471">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="22e29-472">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="22e29-472">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="22e29-473">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-473">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="22e29-474">Razor Pages 規則は、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用して追加および構成され、`Startup` クラスのサービスコレクションで <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> します。</span><span class="sxs-lookup"><span data-stu-id="22e29-474">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="22e29-475">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-475">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="22e29-476">ルートの順序</span><span class="sxs-lookup"><span data-stu-id="22e29-476">Route order</span></span>

<span data-ttu-id="22e29-477">ルートは、処理 (ルート一致) の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="22e29-477">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="22e29-478">Order</span><span class="sxs-lookup"><span data-stu-id="22e29-478">Order</span></span>            | <span data-ttu-id="22e29-479">動作</span><span class="sxs-lookup"><span data-stu-id="22e29-479">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="22e29-480">-1</span><span class="sxs-lookup"><span data-stu-id="22e29-480">-1</span></span>               | <span data-ttu-id="22e29-481">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-481">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="22e29-482">0</span><span class="sxs-lookup"><span data-stu-id="22e29-482">0</span></span>                | <span data-ttu-id="22e29-483">順序が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="22e29-483">Order isn't specified (default value).</span></span> <span data-ttu-id="22e29-484">`Order` (`Order = null`) が割り当てられていない場合、ルートは処理のために 0 (ゼロ) に `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="22e29-484">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="22e29-485">1、2、&hellip; n</span><span class="sxs-lookup"><span data-stu-id="22e29-485">1, 2, &hellip; n</span></span> | <span data-ttu-id="22e29-486">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="22e29-486">Specifies the route processing order.</span></span> |

<span data-ttu-id="22e29-487">ルート処理は規約によって確立されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-487">Route processing is established by convention:</span></span>

* <span data-ttu-id="22e29-488">ルートは順番に処理されます (-1、0、1、2、&hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="22e29-488">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="22e29-489">ルートの `Order`が同じである場合は、最も具体的なルートが最初に照合され、その後に特定のルートがより少なくなります。</span><span class="sxs-lookup"><span data-stu-id="22e29-489">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="22e29-490">同じ `Order` で同じ数のパラメーターが要求 URL と一致する場合、ルートは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-490">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="22e29-491">可能な場合は、確立されたルート処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="22e29-491">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="22e29-492">通常、ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-492">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="22e29-493">要求を正しくルーティングするようにルート `Order` のプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱し、保守が脆弱になることがあります。</span><span class="sxs-lookup"><span data-stu-id="22e29-493">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="22e29-494">アプリのルーティングスキームを簡略化するためにシークします。</span><span class="sxs-lookup"><span data-stu-id="22e29-494">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="22e29-495">このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="22e29-495">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="22e29-496">ただし、運用環境のアプリでルート `Order` を設定する方法を避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="22e29-496">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="22e29-497">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="22e29-497">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="22e29-498">MVC のトピックに記載されている情報については、 [「コントローラーアクションへのルーティング: 属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-498">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="22e29-499">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-499">Model conventions</span></span>

<span data-ttu-id="22e29-500">Razor Pages に適用されるモデルの[規則](xref:mvc/controllers/application-model#conventions)を追加するには、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> のデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-500">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="22e29-501">ルートモデルの規則をすべてのページに追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-501">Add a route model convention to all pages</span></span>

<span data-ttu-id="22e29-502"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページルートモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-502">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="22e29-503">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-503">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-504"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-504">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="22e29-505">これにより、サンプルアプリで次のルート一致動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-505">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="22e29-506">`TheContactPage/{text?}` のルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-506">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="22e29-507">連絡先ページのルートには `null` (`Order = 0`) の既定の順序があるため、`{globalTemplate?}` ルートテンプレートの前に一致します。</span><span class="sxs-lookup"><span data-stu-id="22e29-507">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="22e29-508">`{aboutTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-508">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="22e29-509">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-509">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="22e29-510">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`RouteData.Values["aboutTemplate"]` プロパティが設定されるため `Order = 2` (`Order`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="22e29-510">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="22e29-511">`{otherPagesTemplate?}` ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-511">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="22e29-512">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-512">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="22e29-513">*Pages/otherpages*フォルダー内のいずれかのページがルートパラメーター (`/OtherPages/Page1/RouteDataValue`など) で要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) は読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="22e29-513">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-514">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-514">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-515">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-515">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-516">MVC が `Startup.ConfigureServices`のサービスコレクションに追加されると、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の追加などの Razor Pages オプションが追加されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-516">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="22e29-517">例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-517">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="22e29-518">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-518">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="22e29-521">すべてのページにアプリモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-521">Add an app model convention to all pages</span></span>

<span data-ttu-id="22e29-522"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページアプリモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-522">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="22e29-523">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="22e29-523">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="22e29-524">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="22e29-524">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="22e29-525">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-525">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="22e29-526">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-526">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="22e29-527">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-527">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-528">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-528">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="22e29-529">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-529">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="22e29-531">すべてのページにハンドラーモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="22e29-531">Add a handler model convention to all pages</span></span>

<span data-ttu-id="22e29-532"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページハンドラーモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-532">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="22e29-533">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-533">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="22e29-534">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-534">Page route action conventions</span></span>

<span data-ttu-id="22e29-535"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="22e29-535">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="22e29-536">フォルダールートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-536">Folder route model convention</span></span>

<span data-ttu-id="22e29-537"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページについて <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-537">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="22e29-538">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-538">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="22e29-539"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-539">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="22e29-540">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="22e29-540">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="22e29-541">*Pages/otherpages*フォルダー内のページがルートパラメーター値 (`/OtherPages/Page1/RouteDataValue`など) で要求されている場合、"RouteDataValue" は、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="22e29-541">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-542">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-542">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-543">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-543">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-544">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-544">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="22e29-547">ページルートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="22e29-547">Page route model convention</span></span>

<span data-ttu-id="22e29-548"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-548">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="22e29-549">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-549">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="22e29-550"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-550">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="22e29-551">これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。</span><span class="sxs-lookup"><span data-stu-id="22e29-551">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="22e29-552">`/About/RouteDataValue`でルートパラメーター値を使用して About ページが要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["aboutTemplate"]` (`Order`) ではなくなります。</span><span class="sxs-lookup"><span data-stu-id="22e29-552">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="22e29-553">可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。</span><span class="sxs-lookup"><span data-stu-id="22e29-553">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="22e29-554">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="22e29-554">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="22e29-555">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="22e29-555">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="22e29-558">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="22e29-558">Configure a page route</span></span>

<span data-ttu-id="22e29-559">指定したページパスにあるページへのルートを構成するには、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-559">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="22e29-560">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-560">Generated links to the page use your specified route.</span></span> <span data-ttu-id="22e29-561">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="22e29-561">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="22e29-562">サンプル アプリでは、`/TheContactPage`Contact.cshtml*の* へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-562">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="22e29-563">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="22e29-563">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="22e29-564">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="22e29-564">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="22e29-565">また、訪問者が `@page` ルートでページにアクセスする場合、ページの `/Contact` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="22e29-565">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="22e29-566">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-566">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="22e29-569">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="22e29-569">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="22e29-570">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-570">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="22e29-573">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="22e29-573">Page model action conventions</span></span>

<span data-ttu-id="22e29-574"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページモデルプロバイダーは、ページモデルを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="22e29-574">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="22e29-575">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="22e29-575">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="22e29-576">このセクションの例では、サンプルアプリで <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>である `AddHeaderAttribute` クラスを使用して、応答ヘッダーを適用します。</span><span class="sxs-lookup"><span data-stu-id="22e29-576">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="22e29-577">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-577">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="22e29-578">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="22e29-578">**Folder app model convention**</span></span>

<span data-ttu-id="22e29-579"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスでアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-579">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="22e29-580">このサンプルでは、ヘッダー (`AddFolderApplicationModelConvention`) をアプリの `OtherPagesHeader`OtherPages *フォルダー内にあるページに追加して、* を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-580">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="22e29-581">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-581">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="22e29-583">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="22e29-583">**Page app model convention**</span></span>

<span data-ttu-id="22e29-584"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。</span><span class="sxs-lookup"><span data-stu-id="22e29-584">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="22e29-585">サンプルでは、ヘッダー (`AddPageApplicationModelConvention`) を [About] ページに追加して、`AboutHeader` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="22e29-585">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="22e29-586">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-586">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="22e29-588">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="22e29-588">**Configure a filter**</span></span>

<span data-ttu-id="22e29-589">指定したフィルターを適用するように <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成します。</span><span class="sxs-lookup"><span data-stu-id="22e29-589"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="22e29-590">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-590">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="22e29-591">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-591">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="22e29-592">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-592">If the condition passes, a header is added.</span></span> <span data-ttu-id="22e29-593">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-593">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="22e29-594">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="22e29-594">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="22e29-595">アクションフィルターは Razor Pages によって無視されるため、パスに `OtherPages/Page2`が含まれていない場合、`EmptyFilter` は意図したとおりには効果がありません。</span><span class="sxs-lookup"><span data-stu-id="22e29-595">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="22e29-596">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-596">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="22e29-598">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="22e29-598">**Configure a filter factory**</span></span>

<span data-ttu-id="22e29-599">指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> ます。</span><span class="sxs-lookup"><span data-stu-id="22e29-599"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="22e29-600">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー ([) を追加して、](xref:mvc/controllers/filters#ifilterfactory)フィルター ファクトリ`FilterFactoryHeader`を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="22e29-600">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="22e29-601">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="22e29-601">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="22e29-602">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22e29-602">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="22e29-604">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="22e29-604">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="22e29-605">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="22e29-605">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="22e29-606">その他の型の MVC フィルターは、[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)を使用するために利用できます。</span><span class="sxs-lookup"><span data-stu-id="22e29-606">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="22e29-607">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-607">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="22e29-608">ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。</span><span class="sxs-lookup"><span data-stu-id="22e29-608">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="22e29-609">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22e29-609">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22e29-610">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="22e29-610">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
