---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: rick-anderson
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: f45e327051aba54d1cab67148eb540fb1a5cc149
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651110"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="05993-103">ASP.NET Core での Razor ページのルートとアプリの規則</span><span class="sxs-lookup"><span data-stu-id="05993-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="05993-104">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="05993-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="05993-105">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="05993-106">ページ ルートの指定、ルート セグメントの追加、ルートへのパラメーターの追加を行うには、ページの `@page` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="05993-107">詳しくは、「[カスタム ルート](xref:razor-pages/index#custom-routes)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="05993-108">ルート セグメントやパラメーター名として使用できない予約語がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="05993-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="05993-109">詳しくは、[ルーティング: ルーティングの予約名](xref:fundamentals/routing#reserved-routing-names)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-109">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="05993-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="05993-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="05993-111">シナリオ</span><span class="sxs-lookup"><span data-stu-id="05993-111">Scenario</span></span> | <span data-ttu-id="05993-112">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="05993-113">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="05993-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="05993-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="05993-114">Conventions.Add</span></span><ul><li><span data-ttu-id="05993-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="05993-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="05993-118">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="05993-119">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="05993-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="05993-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="05993-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="05993-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="05993-123">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="05993-124">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="05993-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-127">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="05993-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="05993-128">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="05993-129">Razor ページの規則を追加および構成するには、`Startup` クラス内のサービス コレクションの <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> に <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-129">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="05993-130">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="05993-130">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="05993-131">ルートの順番</span><span class="sxs-lookup"><span data-stu-id="05993-131">Route order</span></span>

<span data-ttu-id="05993-132">ルートは、処理 (ルートの照合) に使われる順番 (<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>) を指定します。</span><span class="sxs-lookup"><span data-stu-id="05993-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="05993-133">順番</span><span class="sxs-lookup"><span data-stu-id="05993-133">Order</span></span>            | <span data-ttu-id="05993-134">動作</span><span class="sxs-lookup"><span data-stu-id="05993-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="05993-135">-1</span><span class="sxs-lookup"><span data-stu-id="05993-135">-1</span></span>               | <span data-ttu-id="05993-136">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="05993-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="05993-137">0</span><span class="sxs-lookup"><span data-stu-id="05993-137">0</span></span>                | <span data-ttu-id="05993-138">順番が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="05993-138">Order isn't specified (default value).</span></span> <span data-ttu-id="05993-139">`Order` を割り当てない (`Order = null`) 場合、処理に使われるルートの `Order` は既定で 0 (ゼロ) になります。</span><span class="sxs-lookup"><span data-stu-id="05993-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="05993-140">1、2、&hellip; n</span><span class="sxs-lookup"><span data-stu-id="05993-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="05993-141">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="05993-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="05993-142">ルートの処理は、次の規則で定められています。</span><span class="sxs-lookup"><span data-stu-id="05993-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="05993-143">ルートは並んだ順番に処理されます (-1、0、1、2、&hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="05993-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="05993-144">ルートの `Order` が同じ場合は、最初に最も明確なルートが照合され、続けて次に明確なルートが照合されます。</span><span class="sxs-lookup"><span data-stu-id="05993-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="05993-145">`Order` とパラメーター数が同じルートが 1 つの要求 URL と一致した場合、ルートは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> に追加された順番で処理されます。</span><span class="sxs-lookup"><span data-stu-id="05993-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="05993-146">可能であれば、定められたルートの処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="05993-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="05993-147">通常は、ルーティングによって、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="05993-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="05993-148">要求が正しくルーティングされるようにルートの `Order` プロパティを設定する必要がある場合、アプリのルーティング方式がクライアントにとって紛らわしいものとなり、保守が脆弱になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="05993-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="05993-149">アプリのルーティング方式を簡略化するよう努めてください。</span><span class="sxs-lookup"><span data-stu-id="05993-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="05993-150">サンプル アプリでは、1 つのアプリで複数のルーティング シナリオを示すために、ルートの明示的な処理順序が必要です。</span><span class="sxs-lookup"><span data-stu-id="05993-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="05993-151">ただし、運用環境のアプリでルートの `Order` を設定するやり方は避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="05993-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="05993-152">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="05993-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="05993-153">MVC のトピックに記載されているルートの順番については、[コントローラー アクションへのルーティング: 属性ルートの順序の指定](xref:mvc/controllers/routing#ordering-attribute-routes)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="05993-154">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="05993-154">Model conventions</span></span>

<span data-ttu-id="05993-155"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> の委任を追加すると、Razor ページに適用される[モデルの規則](xref:mvc/controllers/application-model#conventions)を追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="05993-156">すべてのページにルート モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="05993-157"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して、ページ ルート モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="05993-158">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="05993-160">これにより、サンプル アプリで次のルートの照合動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="05993-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="05993-161">`TheContactPage/{text?}` のルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="05993-162">連絡先ページのルートには既定の順番 `null` (`Order = 0`) が設定されているため、`{globalTemplate?}` ルート テンプレートの前に一致します。</span><span class="sxs-lookup"><span data-stu-id="05993-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="05993-163">`{aboutTemplate?}` ルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="05993-164">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="05993-165">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="05993-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="05993-166">`{otherPagesTemplate?}` ルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="05993-167">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="05993-168">ルート パラメーター (`/OtherPages/Page1/RouteDataValue` など) を使用して *Pages/OtherPages* フォルダー内のいずれかのページが要求されると、`Order` プロパティが設定されているため、"RouteDataValue" が `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-169">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-170">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-171">Razor Pages のオプション (<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> の追加など) は、MVC が `Startup.ConfigureServices` のサービス コレクションに追加されたときに追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05993-172">例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="05993-173">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="05993-176">すべてのページにアプリ モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="05993-177"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して、ページ アプリ モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="05993-178">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="05993-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="05993-179">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="05993-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="05993-180">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="05993-181">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="05993-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="05993-182">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="05993-184">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="05993-186">すべてのページにハンドラー モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="05993-187"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して、ページ ハンドラー モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="05993-189">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-189">Page route action conventions</span></span>

<span data-ttu-id="05993-190"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルート モデル プロバイダーは、ページ ルートを構成するための拡張ポイントを提供するようにデザインされた規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05993-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="05993-191">フォルダー ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="05993-191">Folder route model convention</span></span>

<span data-ttu-id="05993-192"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="05993-193">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="05993-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="05993-195">この設定により、1 つのルート値が指定されたときに、(このトピックの前半で `1` に設定した) `{globalTemplate?}` のテンプレートが優先的に最初のルート データ値の位置に指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="05993-196">ルート パラメーター値 (`/OtherPages/Page1/RouteDataValue` など) を使用して *Pages/OtherPages* フォルダー内のページが要求されると、`Order` プロパティが設定されているため、"RouteDataValue" が `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-197">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-198">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-199">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="05993-202">ページ ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="05993-202">Page route model convention</span></span>

<span data-ttu-id="05993-203"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用すると、指定した名前のページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="05993-204">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="05993-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="05993-206">この設定により、1 つのルート値が指定されたときに、(このトピックの前半で `1` に設定した) `{globalTemplate?}` のテンプレートが優先的に最初のルート データ値の位置に指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="05993-207">[About] ページが `/About/RouteDataValue` にあるルート パラメーター値で要求されると、`Order` プロパティが設定されているため、"RouteDataValue" は `RouteData.Values["aboutTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-208">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-209">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-210">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="05993-213">パラメーター トランスフォーマーを使用してページ ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="05993-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="05993-214">ASP.NET Core によって生成されたページ ルートは、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="05993-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="05993-215">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="05993-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="05993-216">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="05993-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="05993-217">`PageRouteTransformerConvention` ページ ルート モデル規則では、アプリで自動的に生成されたページ ルートのフォルダー名とファイル名のセグメントにパラメーター トランスフォーマーを適用します。</span><span class="sxs-lookup"><span data-stu-id="05993-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="05993-218">たとえば、 */Pages/SubscriptionManagement/ViewAll.cshtml* にある Razor ページ ファイルのルートは `/SubscriptionManagement/ViewAll` から `/subscription-management/view-all` に書き換えられます。</span><span class="sxs-lookup"><span data-stu-id="05993-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="05993-219">`PageRouteTransformerConvention` では、Razor ページのフォルダー名とファイル名に由来する、自動的に生成されたページ ルートのセグメントのみを変換します。</span><span class="sxs-lookup"><span data-stu-id="05993-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="05993-220">`@page` ディレクティブを使用して追加したルート セグメントは変換されません。</span><span class="sxs-lookup"><span data-stu-id="05993-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="05993-221">この規則では、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> で追加したルートも変換されません。</span><span class="sxs-lookup"><span data-stu-id="05993-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="05993-222">`PageRouteTransformerConvention` は、`Startup.ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="05993-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="05993-223">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="05993-223">Configure a page route</span></span>

<span data-ttu-id="05993-224"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用すると、指定したページ パスにあるページへのルートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="05993-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="05993-225">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="05993-226">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="05993-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="05993-227">サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="05993-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="05993-228">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="05993-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="05993-229">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="05993-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="05993-230">また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="05993-231">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="05993-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="05993-234">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="05993-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="05993-235">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="05993-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="05993-238">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-238">Page model action conventions</span></span>

<span data-ttu-id="05993-239"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページ モデル プロバイダーは、ページ モデルを構成するための拡張ポイントを提供するようにデザインされた規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05993-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="05993-240">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="05993-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="05993-241">このセクションの例の場合、サンプル アプリでは、応答ヘッダーを適用する <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> である、`AddHeaderAttribute` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="05993-242">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="05993-243">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="05993-243">**Folder app model convention**</span></span>

<span data-ttu-id="05993-244"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスへのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="05993-245">このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="05993-246">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="05993-248">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="05993-248">**Page app model convention**</span></span>

<span data-ttu-id="05993-249"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用すると、指定した名前のページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="05993-250">サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="05993-251">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="05993-253">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="05993-253">**Configure a filter**</span></span>

<span data-ttu-id="05993-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> では、指定したフィルターを適用するように構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="05993-255">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="05993-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="05993-256">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="05993-257">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="05993-258">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="05993-259">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="05993-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="05993-260">アクション フィルターは Razor ページによって無視されるため、パスに `OtherPages/Page2` が含まれない場合、`EmptyFilter` には意図されたような効果はありません。</span><span class="sxs-lookup"><span data-stu-id="05993-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="05993-261">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="05993-263">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="05993-263">**Configure a filter factory**</span></span>

<span data-ttu-id="05993-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> では、すべての Razor ページに[フィルター](xref:mvc/controllers/filters)を適用するように、指定したファクトリを構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="05993-265">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="05993-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="05993-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="05993-267">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="05993-269">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="05993-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="05993-270">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="05993-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="05993-271">使用できるその他の種類の MVC フィルターは次のとおりです。[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="05993-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="05993-272">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="05993-273">ページ フィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor ページに適用されるフィルターの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="05993-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="05993-274">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05993-275">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="05993-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="05993-276">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="05993-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="05993-277">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="05993-278">ページ ルートの指定、ルート セグメントの追加、ルートへのパラメーターの追加を行うには、ページの `@page` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="05993-279">詳しくは、「[カスタム ルート](xref:razor-pages/index#custom-routes)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="05993-280">ルート セグメントやパラメーター名として使用できない予約語がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="05993-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="05993-281">詳しくは、[ルーティング: ルーティングの予約名](xref:fundamentals/routing#reserved-routing-names)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="05993-282">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="05993-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="05993-283">シナリオ</span><span class="sxs-lookup"><span data-stu-id="05993-283">Scenario</span></span> | <span data-ttu-id="05993-284">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="05993-285">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="05993-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="05993-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="05993-286">Conventions.Add</span></span><ul><li><span data-ttu-id="05993-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="05993-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="05993-290">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="05993-291">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="05993-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="05993-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="05993-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="05993-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="05993-295">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="05993-296">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="05993-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-299">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="05993-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="05993-300">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="05993-301">Razor ページの規則を追加および構成するには、`Startup` クラス内のサービス コレクションの <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> に <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="05993-302">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="05993-302">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="05993-303">ルートの順番</span><span class="sxs-lookup"><span data-stu-id="05993-303">Route order</span></span>

<span data-ttu-id="05993-304">ルートは、処理 (ルートの照合) に使われる順番 (<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>) を指定します。</span><span class="sxs-lookup"><span data-stu-id="05993-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="05993-305">順番</span><span class="sxs-lookup"><span data-stu-id="05993-305">Order</span></span>            | <span data-ttu-id="05993-306">動作</span><span class="sxs-lookup"><span data-stu-id="05993-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="05993-307">-1</span><span class="sxs-lookup"><span data-stu-id="05993-307">-1</span></span>               | <span data-ttu-id="05993-308">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="05993-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="05993-309">0</span><span class="sxs-lookup"><span data-stu-id="05993-309">0</span></span>                | <span data-ttu-id="05993-310">順番が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="05993-310">Order isn't specified (default value).</span></span> <span data-ttu-id="05993-311">`Order` を割り当てない (`Order = null`) 場合、処理に使われるルートの `Order` は既定で 0 (ゼロ) になります。</span><span class="sxs-lookup"><span data-stu-id="05993-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="05993-312">1、2、&hellip; n</span><span class="sxs-lookup"><span data-stu-id="05993-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="05993-313">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="05993-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="05993-314">ルートの処理は、次の規則で定められています。</span><span class="sxs-lookup"><span data-stu-id="05993-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="05993-315">ルートは並んだ順番に処理されます (-1、0、1、2、&hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="05993-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="05993-316">ルートの `Order` が同じ場合は、最初に最も明確なルートが照合され、続けて次に明確なルートが照合されます。</span><span class="sxs-lookup"><span data-stu-id="05993-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="05993-317">`Order` とパラメーター数が同じルートが 1 つの要求 URL と一致した場合、ルートは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> に追加された順番で処理されます。</span><span class="sxs-lookup"><span data-stu-id="05993-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="05993-318">可能であれば、定められたルートの処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="05993-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="05993-319">通常は、ルーティングによって、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="05993-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="05993-320">要求が正しくルーティングされるようにルートの `Order` プロパティを設定する必要がある場合、アプリのルーティング方式がクライアントにとって紛らわしいものとなり、保守が脆弱になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="05993-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="05993-321">アプリのルーティング方式を簡略化するよう努めてください。</span><span class="sxs-lookup"><span data-stu-id="05993-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="05993-322">サンプル アプリでは、1 つのアプリで複数のルーティング シナリオを示すために、ルートの明示的な処理順序が必要です。</span><span class="sxs-lookup"><span data-stu-id="05993-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="05993-323">ただし、運用環境のアプリでルートの `Order` を設定するやり方は避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="05993-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="05993-324">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="05993-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="05993-325">MVC のトピックに記載されているルートの順番については、[コントローラー アクションへのルーティング: 属性ルートの順序の指定](xref:mvc/controllers/routing#ordering-attribute-routes)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="05993-326">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="05993-326">Model conventions</span></span>

<span data-ttu-id="05993-327"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> の委任を追加すると、Razor ページに適用される[モデルの規則](xref:mvc/controllers/application-model#conventions)を追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="05993-328">すべてのページにルート モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="05993-329"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して、ページ ルート モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="05993-330">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="05993-332">これにより、サンプル アプリで次のルートの照合動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="05993-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="05993-333">`TheContactPage/{text?}` のルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="05993-334">連絡先ページのルートには既定の順番 `null` (`Order = 0`) が設定されているため、`{globalTemplate?}` ルート テンプレートの前に一致します。</span><span class="sxs-lookup"><span data-stu-id="05993-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="05993-335">`{aboutTemplate?}` ルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="05993-336">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="05993-337">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="05993-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="05993-338">`{otherPagesTemplate?}` ルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="05993-339">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="05993-340">ルート パラメーター (`/OtherPages/Page1/RouteDataValue` など) を使用して *Pages/OtherPages* フォルダー内のいずれかのページが要求されると、`Order` プロパティが設定されているため、"RouteDataValue" が `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-341">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-342">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-343">Razor Pages のオプション (<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> の追加など) は、MVC が `Startup.ConfigureServices` のサービス コレクションに追加されたときに追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05993-344">例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="05993-345">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="05993-348">すべてのページにアプリ モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="05993-349"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して、ページ アプリ モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="05993-350">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="05993-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="05993-351">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="05993-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="05993-352">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="05993-353">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="05993-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="05993-354">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="05993-356">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="05993-358">すべてのページにハンドラー モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="05993-359"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して、ページ ハンドラー モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="05993-361">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-361">Page route action conventions</span></span>

<span data-ttu-id="05993-362"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルート モデル プロバイダーは、ページ ルートを構成するための拡張ポイントを提供するようにデザインされた規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05993-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="05993-363">フォルダー ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="05993-363">Folder route model convention</span></span>

<span data-ttu-id="05993-364"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="05993-365">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="05993-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="05993-367">この設定により、1 つのルート値が指定されたときに、(このトピックの前半で `1` に設定した) `{globalTemplate?}` のテンプレートが優先的に最初のルート データ値の位置に指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="05993-368">ルート パラメーター値 (`/OtherPages/Page1/RouteDataValue` など) を使用して *Pages/OtherPages* フォルダー内のページが要求されると、`Order` プロパティが設定されているため、"RouteDataValue" が `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-369">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-370">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-371">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="05993-374">ページ ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="05993-374">Page route model convention</span></span>

<span data-ttu-id="05993-375"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用すると、指定した名前のページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="05993-376">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="05993-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="05993-378">この設定により、1 つのルート値が指定されたときに、(このトピックの前半で `1` に設定した) `{globalTemplate?}` のテンプレートが優先的に最初のルート データ値の位置に指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="05993-379">[About] ページが `/About/RouteDataValue` にあるルート パラメーター値で要求されると、`Order` プロパティが設定されているため、"RouteDataValue" は `RouteData.Values["aboutTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-380">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-381">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-382">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="05993-385">パラメーター トランスフォーマーを使用してページ ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="05993-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="05993-386">ASP.NET Core によって生成されたページ ルートは、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="05993-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="05993-387">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="05993-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="05993-388">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="05993-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="05993-389">`PageRouteTransformerConvention` ページ ルート モデル規則では、アプリで自動的に生成されたページ ルートのフォルダー名とファイル名のセグメントにパラメーター トランスフォーマーを適用します。</span><span class="sxs-lookup"><span data-stu-id="05993-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="05993-390">たとえば、 */Pages/SubscriptionManagement/ViewAll.cshtml* にある Razor ページ ファイルのルートは `/SubscriptionManagement/ViewAll` から `/subscription-management/view-all` に書き換えられます。</span><span class="sxs-lookup"><span data-stu-id="05993-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="05993-391">`PageRouteTransformerConvention` では、Razor ページのフォルダー名とファイル名に由来する、自動的に生成されたページ ルートのセグメントのみを変換します。</span><span class="sxs-lookup"><span data-stu-id="05993-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="05993-392">`@page` ディレクティブを使用して追加したルート セグメントは変換されません。</span><span class="sxs-lookup"><span data-stu-id="05993-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="05993-393">この規則では、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> で追加したルートも変換されません。</span><span class="sxs-lookup"><span data-stu-id="05993-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="05993-394">`PageRouteTransformerConvention` は、`Startup.ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="05993-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="05993-395">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="05993-395">Configure a page route</span></span>

<span data-ttu-id="05993-396"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用すると、指定したページ パスにあるページへのルートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="05993-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="05993-397">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="05993-398">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="05993-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="05993-399">サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="05993-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="05993-400">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="05993-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="05993-401">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="05993-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="05993-402">また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="05993-403">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="05993-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="05993-406">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="05993-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="05993-407">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="05993-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="05993-410">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-410">Page model action conventions</span></span>

<span data-ttu-id="05993-411"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページ モデル プロバイダーは、ページ モデルを構成するための拡張ポイントを提供するようにデザインされた規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05993-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="05993-412">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="05993-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="05993-413">このセクションの例の場合、サンプル アプリでは、応答ヘッダーを適用する <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> である、`AddHeaderAttribute` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="05993-414">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="05993-415">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="05993-415">**Folder app model convention**</span></span>

<span data-ttu-id="05993-416"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスへのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="05993-417">このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="05993-418">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="05993-420">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="05993-420">**Page app model convention**</span></span>

<span data-ttu-id="05993-421"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用すると、指定した名前のページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="05993-422">サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="05993-423">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="05993-425">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="05993-425">**Configure a filter**</span></span>

<span data-ttu-id="05993-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> では、指定したフィルターを適用するように構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="05993-427">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="05993-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="05993-428">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="05993-429">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="05993-430">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="05993-431">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="05993-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="05993-432">アクション フィルターは Razor ページによって無視されるため、パスに `OtherPages/Page2` が含まれない場合、`EmptyFilter` には意図されたような効果はありません。</span><span class="sxs-lookup"><span data-stu-id="05993-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="05993-433">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="05993-435">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="05993-435">**Configure a filter factory**</span></span>

<span data-ttu-id="05993-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> では、すべての Razor ページに[フィルター](xref:mvc/controllers/filters)を適用するように、指定したファクトリを構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="05993-437">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="05993-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="05993-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="05993-439">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="05993-441">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="05993-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="05993-442">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="05993-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="05993-443">使用できるその他の種類の MVC フィルターは次のとおりです。[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="05993-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="05993-444">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="05993-445">ページ フィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor ページに適用されるフィルターの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="05993-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="05993-446">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05993-447">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="05993-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="05993-448">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="05993-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="05993-449">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="05993-450">ページ ルートの指定、ルート セグメントの追加、ルートへのパラメーターの追加を行うには、ページの `@page` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="05993-451">詳しくは、「[カスタム ルート](xref:razor-pages/index#custom-routes)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="05993-452">ルート セグメントやパラメーター名として使用できない予約語がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="05993-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="05993-453">詳しくは、[ルーティング: ルーティングの予約名](xref:fundamentals/routing#reserved-routing-names)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="05993-454">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="05993-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="05993-455">シナリオ</span><span class="sxs-lookup"><span data-stu-id="05993-455">Scenario</span></span> | <span data-ttu-id="05993-456">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="05993-457">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="05993-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="05993-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="05993-458">Conventions.Add</span></span><ul><li><span data-ttu-id="05993-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="05993-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="05993-462">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="05993-463">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="05993-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="05993-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="05993-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="05993-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="05993-467">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="05993-468">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="05993-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="05993-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="05993-471">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="05993-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="05993-472">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="05993-473">Razor ページの規則を追加および構成するには、`Startup` クラス内のサービス コレクションの <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> に <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="05993-474">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="05993-474">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="05993-475">ルートの順番</span><span class="sxs-lookup"><span data-stu-id="05993-475">Route order</span></span>

<span data-ttu-id="05993-476">ルートは、処理 (ルートの照合) に使われる順番 (<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>) を指定します。</span><span class="sxs-lookup"><span data-stu-id="05993-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="05993-477">順番</span><span class="sxs-lookup"><span data-stu-id="05993-477">Order</span></span>            | <span data-ttu-id="05993-478">動作</span><span class="sxs-lookup"><span data-stu-id="05993-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="05993-479">-1</span><span class="sxs-lookup"><span data-stu-id="05993-479">-1</span></span>               | <span data-ttu-id="05993-480">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="05993-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="05993-481">0</span><span class="sxs-lookup"><span data-stu-id="05993-481">0</span></span>                | <span data-ttu-id="05993-482">順番が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="05993-482">Order isn't specified (default value).</span></span> <span data-ttu-id="05993-483">`Order` を割り当てない (`Order = null`) 場合、処理に使われるルートの `Order` は既定で 0 (ゼロ) になります。</span><span class="sxs-lookup"><span data-stu-id="05993-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="05993-484">1、2、&hellip; n</span><span class="sxs-lookup"><span data-stu-id="05993-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="05993-485">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="05993-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="05993-486">ルートの処理は、次の規則で定められています。</span><span class="sxs-lookup"><span data-stu-id="05993-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="05993-487">ルートは並んだ順番に処理されます (-1、0、1、2、&hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="05993-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="05993-488">ルートの `Order` が同じ場合は、最初に最も明確なルートが照合され、続けて次に明確なルートが照合されます。</span><span class="sxs-lookup"><span data-stu-id="05993-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="05993-489">`Order` とパラメーター数が同じルートが 1 つの要求 URL と一致した場合、ルートは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> に追加された順番で処理されます。</span><span class="sxs-lookup"><span data-stu-id="05993-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="05993-490">可能であれば、定められたルートの処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="05993-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="05993-491">通常は、ルーティングによって、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="05993-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="05993-492">要求が正しくルーティングされるようにルートの `Order` プロパティを設定する必要がある場合、アプリのルーティング方式がクライアントにとって紛らわしいものとなり、保守が脆弱になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="05993-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="05993-493">アプリのルーティング方式を簡略化するよう努めてください。</span><span class="sxs-lookup"><span data-stu-id="05993-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="05993-494">サンプル アプリでは、1 つのアプリで複数のルーティング シナリオを示すために、ルートの明示的な処理順序が必要です。</span><span class="sxs-lookup"><span data-stu-id="05993-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="05993-495">ただし、運用環境のアプリでルートの `Order` を設定するやり方は避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="05993-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="05993-496">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="05993-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="05993-497">MVC のトピックに記載されているルートの順番については、[コントローラー アクションへのルーティング: 属性ルートの順序の指定](xref:mvc/controllers/routing#ordering-attribute-routes)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05993-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="05993-498">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="05993-498">Model conventions</span></span>

<span data-ttu-id="05993-499"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> の委任を追加すると、Razor ページに適用される[モデルの規則](xref:mvc/controllers/application-model#conventions)を追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="05993-500">すべてのページにルート モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="05993-501"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して、ページ ルート モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="05993-502">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="05993-504">これにより、サンプル アプリで次のルートの照合動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="05993-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="05993-505">`TheContactPage/{text?}` のルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="05993-506">連絡先ページのルートには既定の順番 `null` (`Order = 0`) が設定されているため、`{globalTemplate?}` ルート テンプレートの前に一致します。</span><span class="sxs-lookup"><span data-stu-id="05993-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="05993-507">`{aboutTemplate?}` ルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="05993-508">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="05993-509">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="05993-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="05993-510">`{otherPagesTemplate?}` ルート テンプレートは、このトピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="05993-511">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="05993-512">ルート パラメーター (`/OtherPages/Page1/RouteDataValue` など) を使用して *Pages/OtherPages* フォルダー内のいずれかのページが要求されると、`Order` プロパティが設定されているため、"RouteDataValue" が `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-513">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-514">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-515">Razor Pages のオプション (<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> の追加など) は、MVC が `Startup.ConfigureServices` のサービス コレクションに追加されたときに追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05993-516">例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="05993-517">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="05993-520">すべてのページにアプリ モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="05993-521"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して、ページ アプリ モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="05993-522">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="05993-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="05993-523">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="05993-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="05993-524">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="05993-525">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="05993-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="05993-526">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="05993-528">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="05993-530">すべてのページにハンドラー モデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="05993-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="05993-531"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用すると、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して、ページ ハンドラー モデルの構築中に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="05993-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="05993-533">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-533">Page route action conventions</span></span>

<span data-ttu-id="05993-534"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルート モデル プロバイダーは、ページ ルートを構成するための拡張ポイントを提供するようにデザインされた規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05993-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="05993-535">フォルダー ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="05993-535">Folder route model convention</span></span>

<span data-ttu-id="05993-536"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="05993-537">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="05993-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="05993-539">この設定により、1 つのルート値が指定されたときに、(このトピックの前半で `1` に設定した) `{globalTemplate?}` のテンプレートが優先的に最初のルート データ値の位置に指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="05993-540">ルート パラメーター値 (`/OtherPages/Page1/RouteDataValue` など) を使用して *Pages/OtherPages* フォルダー内のページが要求されると、`Order` プロパティが設定されているため、"RouteDataValue" が `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-541">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-542">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-543">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="05993-546">ページ ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="05993-546">Page route model convention</span></span>

<span data-ttu-id="05993-547"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用すると、指定した名前のページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="05993-548">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="05993-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="05993-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="05993-550">この設定により、1 つのルート値が指定されたときに、(このトピックの前半で `1` に設定した) `{globalTemplate?}` のテンプレートが優先的に最初のルート データ値の位置に指定されます。</span><span class="sxs-lookup"><span data-stu-id="05993-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="05993-551">[About] ページが `/About/RouteDataValue` にあるルート パラメーター値で要求されると、`Order` プロパティが設定されているため、"RouteDataValue" は `RouteData.Values["aboutTemplate"]` (`Order = 2`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="05993-552">可能な限り、`Order` を設定しないでください。これにより、`Order = 0` が生じます。</span><span class="sxs-lookup"><span data-stu-id="05993-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="05993-553">正しいルートの選択には、ルーティングを使用してください。</span><span class="sxs-lookup"><span data-stu-id="05993-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="05993-554">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="05993-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="05993-557">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="05993-557">Configure a page route</span></span>

<span data-ttu-id="05993-558"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用すると、指定したページ パスにあるページへのルートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="05993-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="05993-559">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="05993-560">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="05993-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="05993-561">サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="05993-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="05993-562">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="05993-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="05993-563">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="05993-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="05993-564">また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="05993-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="05993-565">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="05993-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="05993-568">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="05993-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="05993-569">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="05993-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="05993-572">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="05993-572">Page model action conventions</span></span>

<span data-ttu-id="05993-573"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページ モデル プロバイダーは、ページ モデルを構成するための拡張ポイントを提供するようにデザインされた規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05993-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="05993-574">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="05993-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="05993-575">このセクションの例の場合、サンプル アプリでは、応答ヘッダーを適用する <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> である、`AddHeaderAttribute` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="05993-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="05993-576">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="05993-577">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="05993-577">**Folder app model convention**</span></span>

<span data-ttu-id="05993-578"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスへのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="05993-579">このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="05993-580">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="05993-582">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="05993-582">**Page app model convention**</span></span>

<span data-ttu-id="05993-583"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用すると、指定した名前のページを対象に <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> へのアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。</span><span class="sxs-lookup"><span data-stu-id="05993-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="05993-584">サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="05993-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="05993-585">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="05993-587">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="05993-587">**Configure a filter**</span></span>

<span data-ttu-id="05993-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> では、指定したフィルターを適用するように構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="05993-589">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="05993-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="05993-590">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="05993-591">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="05993-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="05993-592">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="05993-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="05993-593">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="05993-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="05993-594">アクション フィルターは Razor ページによって無視されるため、パスに `OtherPages/Page2` が含まれない場合、`EmptyFilter` には意図されたような効果はありません。</span><span class="sxs-lookup"><span data-stu-id="05993-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="05993-595">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="05993-597">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="05993-597">**Configure a filter factory**</span></span>

<span data-ttu-id="05993-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> では、すべての Razor ページに[フィルター](xref:mvc/controllers/filters)を適用するように、指定したファクトリを構成します。</span><span class="sxs-lookup"><span data-stu-id="05993-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="05993-599">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="05993-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="05993-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="05993-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="05993-601">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="05993-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="05993-603">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="05993-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="05993-604">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="05993-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="05993-605">使用できるその他の種類の MVC フィルターは次のとおりです。[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="05993-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="05993-606">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="05993-607">ページ フィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor ページに適用されるフィルターの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="05993-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="05993-608">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="05993-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05993-609">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="05993-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
