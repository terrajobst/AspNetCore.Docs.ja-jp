---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: guardrex
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 59c8af648b50deb51f3762c14348d08acd48886e
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724452"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="83095-103">ASP.NET Core での Razor ページのルートとアプリの規則</span><span class="sxs-lookup"><span data-stu-id="83095-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="83095-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="83095-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="83095-105">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="83095-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="83095-106">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="83095-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="83095-107">ページ ルートを指定、ルート セグメントを追加またはルートにパラメーターを追加使用ページの`@page`ディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="83095-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="83095-108">詳細については、次を参照してください。[カスタム ルート](xref:razor-pages/index#custom-routes)します。</span><span class="sxs-lookup"><span data-stu-id="83095-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="83095-109">ルート セグメントまたはパラメーター名として使用できません。 予約語があります。</span><span class="sxs-lookup"><span data-stu-id="83095-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="83095-110">詳細については、次を参照してください。[ルーティング。ルーティングの名前を予約](xref:fundamentals/routing#reserved-routing-names)します。</span><span class="sxs-lookup"><span data-stu-id="83095-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="83095-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="83095-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="83095-112">シナリオ</span><span class="sxs-lookup"><span data-stu-id="83095-112">Scenario</span></span> | <span data-ttu-id="83095-113">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="83095-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="83095-114">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="83095-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="83095-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="83095-115">Conventions.Add</span></span><ul><li><span data-ttu-id="83095-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="83095-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="83095-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="83095-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="83095-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="83095-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="83095-119">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="83095-120">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="83095-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="83095-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="83095-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="83095-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="83095-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="83095-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="83095-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="83095-124">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="83095-125">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="83095-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="83095-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="83095-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="83095-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="83095-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="83095-128">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="83095-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="83095-129">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="83095-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="83095-130">Razor ページの規則が追加され、構成を使用して、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>拡張メソッドを<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>でサービスのコレクションを`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="83095-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="83095-131">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="83095-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="83095-132">ルートの順序</span><span class="sxs-lookup"><span data-stu-id="83095-132">Route order</span></span>

<span data-ttu-id="83095-133">ルートの指定、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (ルートの一致) を処理するためです。</span><span class="sxs-lookup"><span data-stu-id="83095-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="83095-134">注文</span><span class="sxs-lookup"><span data-stu-id="83095-134">Order</span></span>            | <span data-ttu-id="83095-135">動作</span><span class="sxs-lookup"><span data-stu-id="83095-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="83095-136">-1</span><span class="sxs-lookup"><span data-stu-id="83095-136">-1</span></span>               | <span data-ttu-id="83095-137">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="83095-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="83095-138">0</span><span class="sxs-lookup"><span data-stu-id="83095-138">0</span></span>                | <span data-ttu-id="83095-139">順序が指定されていない (既定値)。</span><span class="sxs-lookup"><span data-stu-id="83095-139">Order isn't specified (default value).</span></span> <span data-ttu-id="83095-140">割り当てない`Order`(`Order = null`) 既定値は、ルート`Order`を処理するための 0 (ゼロ)。</span><span class="sxs-lookup"><span data-stu-id="83095-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="83095-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="83095-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="83095-142">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="83095-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="83095-143">ルートの処理規則を確立するには。</span><span class="sxs-lookup"><span data-stu-id="83095-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="83095-144">ルートが順番に処理される (-1、0、1、2、 &hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="83095-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="83095-145">ルートがある同じ`Order`最も低い特定のルートの順に特定のルートが一致します。</span><span class="sxs-lookup"><span data-stu-id="83095-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="83095-146">ときに、同じルート`Order`と同じ数のパラメーターが要求 URL と一致、ルートに追加された順序で処理、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>します。</span><span class="sxs-lookup"><span data-stu-id="83095-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="83095-147">可能であれば、確立されたルートの処理順序に応じてしないでください。</span><span class="sxs-lookup"><span data-stu-id="83095-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="83095-148">一般に、ルーティングが URL に一致する適切なルートを選択します。</span><span class="sxs-lookup"><span data-stu-id="83095-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="83095-149">ルートを設定する必要がありますと`Order`をルーティングするプロパティの要求を正しく、アプリのルーティング スキームは、おそらくクライアントに混乱を維持するために脆弱です。</span><span class="sxs-lookup"><span data-stu-id="83095-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="83095-150">アプリのルーティング スキームを簡略化しようとしてください。</span><span class="sxs-lookup"><span data-stu-id="83095-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="83095-151">サンプル アプリでは、明示的なルートを 1 つのアプリを使用してルーティングのシナリオをいくつかを示すために注文の処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="83095-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="83095-152">ただし、設定のルートのプラクティスを回避しようとする必要があります`Order`運用アプリでします。</span><span class="sxs-lookup"><span data-stu-id="83095-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="83095-153">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="83095-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="83095-154">MVC のトピックでルートの順序については、「[コント ローラー アクションへのルーティング。属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)します。</span><span class="sxs-lookup"><span data-stu-id="83095-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="83095-155">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="83095-155">Model conventions</span></span>

<span data-ttu-id="83095-156">デリゲートを追加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>を追加する[モデル規則](xref:mvc/controllers/application-model#conventions)Razor ページに適用されています。</span><span class="sxs-lookup"><span data-stu-id="83095-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="83095-157">すべてのページにルート モデル規則を追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="83095-158">使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>のコレクションに<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>ページ ルートの中に適用されるインスタンスの構築をモデル化します。</span><span class="sxs-lookup"><span data-stu-id="83095-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="83095-159">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="83095-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="83095-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="83095-161">これにより、次のルートが一致のサンプル アプリで動作します。</span><span class="sxs-lookup"><span data-stu-id="83095-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="83095-162">ルート テンプレート`TheContactPage/{text?}`トピックの後半で追加されます。</span><span class="sxs-lookup"><span data-stu-id="83095-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="83095-163">Contact ページのルートの既定の順序を持つ`null`(`Order = 0`) する前に、一致するよう、`{globalTemplate?}`ルート テンプレート。</span><span class="sxs-lookup"><span data-stu-id="83095-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="83095-164">`{aboutTemplate?}`トピックの後半でルート テンプレートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="83095-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="83095-165">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="83095-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="83095-166">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="83095-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="83095-167">`{otherPagesTemplate?}`トピックの後半でルート テンプレートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="83095-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="83095-168">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="83095-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="83095-169">いずれかのページ、*ページ/OtherPages*フォルダーはルート パラメーターで要求されます (たとえば、 `/OtherPages/Page1/RouteDataValue`)、"RouteDataValue"に読み込まれる`RouteData.Values["globalTemplate"]`(`Order = 1`) および not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`)設定により、`Order`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="83095-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="83095-170">可能であれば、任意の場所を設定しないでください、 `Order`、その結果は`Order = 0`します。</span><span class="sxs-lookup"><span data-stu-id="83095-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="83095-171">適切なルートを選択するルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="83095-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="83095-172">追加するなどの razor ページ オプション<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>、MVC がサービス コレクションに追加されたときに追加されます`Startup.ConfigureServices`します。</span><span class="sxs-lookup"><span data-stu-id="83095-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="83095-173">例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83095-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="83095-174">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="83095-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="83095-177">すべてのページに、アプリ モデル規則を追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="83095-178">使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>のコレクションに<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>中に適用されるインスタンスのページ アプリ モデルの構築。</span><span class="sxs-lookup"><span data-stu-id="83095-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="83095-179">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="83095-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="83095-180">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="83095-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="83095-181">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="83095-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="83095-182">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="83095-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="83095-183">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="83095-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="83095-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="83095-185">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="83095-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="83095-187">すべてのページに、ハンドラーのモデル規則を追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="83095-188">使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>のコレクションに<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>中に適用されるインスタンスのページ ハンドラーのモデルの構築。</span><span class="sxs-lookup"><span data-stu-id="83095-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="83095-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="83095-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="83095-190">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="83095-190">Page route action conventions</span></span>

<span data-ttu-id="83095-191">派生した既定のルート モデル プロバイダー<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>ページ ルートを構成するための機能拡張ポイントを提供するように設計されている規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="83095-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="83095-192">フォルダー ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="83095-192">Folder route model convention</span></span>

<span data-ttu-id="83095-193">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>上のアクションを呼び出す、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>のページにある、指定したフォルダーのすべての。</span><span class="sxs-lookup"><span data-stu-id="83095-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="83095-194">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="83095-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="83095-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="83095-196">これにより、テンプレートを`{globalTemplate?}`(にこのトピックの設定`1`) 単一のルート値を指定した場合の最初のルート データ値の位置が優先されます。</span><span class="sxs-lookup"><span data-stu-id="83095-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="83095-197">場合内のページ、*ページ/OtherPages*フォルダーはルート パラメーターの値で要求されます (たとえば、 `/OtherPages/Page1/RouteDataValue`)、"RouteDataValue"は読み込ま`RouteData.Values["globalTemplate"]`(`Order = 1`) および not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`)設定により、`Order`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="83095-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="83095-198">可能であれば、任意の場所を設定しないでください、 `Order`、その結果は`Order = 0`します。</span><span class="sxs-lookup"><span data-stu-id="83095-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="83095-199">適切なルートを選択するルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="83095-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="83095-200">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="83095-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="83095-203">ページ ルート モデル規則</span><span class="sxs-lookup"><span data-stu-id="83095-203">Page route model convention</span></span>

<span data-ttu-id="83095-204">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>上のアクションを呼び出す、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>の指定した名前のページ。</span><span class="sxs-lookup"><span data-stu-id="83095-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="83095-205">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83095-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="83095-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="83095-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="83095-207">これにより、テンプレートを`{globalTemplate?}`(にこのトピックの設定`1`) 単一のルート値を指定した場合の最初のルート データ値の位置が優先されます。</span><span class="sxs-lookup"><span data-stu-id="83095-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="83095-208">[About] ページがあるルート パラメーター値で要求されたかどうか`/About/RouteDataValue`、"RouteDataValue"は読み込ま`RouteData.Values["globalTemplate"]`(`Order = 1`) および not `RouteData.Values["aboutTemplate"]` (`Order = 2`) 設定により、`Order`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="83095-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="83095-209">可能であれば、任意の場所を設定しないでください、 `Order`、その結果は`Order = 0`します。</span><span class="sxs-lookup"><span data-stu-id="83095-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="83095-210">適切なルートを選択するルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="83095-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="83095-211">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="83095-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="83095-214">パラメーターのトランスフォーマーを使用して、ページ ルートをカスタマイズするには</span><span class="sxs-lookup"><span data-stu-id="83095-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="83095-215">ASP.NET Core によって生成されたページのルートは、パラメーターのトランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="83095-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="83095-216">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="83095-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="83095-217">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="83095-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="83095-218">`PageRouteTransformerConvention`ページ ルート モデル規則では、アプリのルートを自動的に生成されたページのフォルダーとファイル名のセグメントにパラメーターのトランスフォーマーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="83095-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="83095-219">たとえば、Razor ページのファイルを */Pages/SubscriptionManagement/ViewAll.cshtml*のルートから書き直す必要が`/SubscriptionManagement/ViewAll`に`/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="83095-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="83095-220">`PageRouteTransformerConvention` Razor ページのフォルダーとファイル名から取得したページ ルートを自動的に生成されたセグメントを変換するだけです。</span><span class="sxs-lookup"><span data-stu-id="83095-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="83095-221">使用して追加のルート セグメントを変換されません、`@page`ディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="83095-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="83095-222">規則もによって追加されたルートを変換しない<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>します。</span><span class="sxs-lookup"><span data-stu-id="83095-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="83095-223">`PageRouteTransformerConvention`オプションとして登録されて`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="83095-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="83095-224">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="83095-224">Configure a page route</span></span>

<span data-ttu-id="83095-225">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>指定したページのパスにあるページにルートを構成します。</span><span class="sxs-lookup"><span data-stu-id="83095-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="83095-226">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="83095-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="83095-227">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="83095-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="83095-228">サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="83095-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="83095-229">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="83095-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="83095-230">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="83095-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="83095-231">また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="83095-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="83095-232">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="83095-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="83095-235">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="83095-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="83095-236">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="83095-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="83095-239">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="83095-239">Page model action conventions</span></span>

<span data-ttu-id="83095-240">既定のページ モデル プロバイダーを実装する<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>ページ モデルを構成するための機能拡張ポイントを提供するように設計されている規則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="83095-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="83095-241">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="83095-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="83095-242">このセクションの例については、サンプル アプリを使用して、`AddHeaderAttribute`はクラス、 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>、応答ヘッダーを適用します。</span><span class="sxs-lookup"><span data-stu-id="83095-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="83095-243">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="83095-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="83095-244">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="83095-244">**Folder app model convention**</span></span>

<span data-ttu-id="83095-245">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>上のアクションを呼び出す<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>の指定したフォルダーの下のすべてのページ インスタンス。</span><span class="sxs-lookup"><span data-stu-id="83095-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="83095-246">このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="83095-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="83095-247">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="83095-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="83095-249">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="83095-249">**Page app model convention**</span></span>

<span data-ttu-id="83095-250">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>上のアクションを呼び出す、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>の指定した名前のページ。</span><span class="sxs-lookup"><span data-stu-id="83095-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="83095-251">サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="83095-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="83095-252">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="83095-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="83095-254">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="83095-254">**Configure a filter**</span></span>

<span data-ttu-id="83095-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 指定したフィルターを適用するを構成します。</span><span class="sxs-lookup"><span data-stu-id="83095-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="83095-256">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="83095-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="83095-257">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="83095-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="83095-258">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="83095-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="83095-259">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="83095-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="83095-260">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="83095-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="83095-261">アクション フィルターは、Razor ページによって無視されますので、`EmptyFilter`パスが含まれていないかどうかは意図どおりに影響を与えません`OtherPages/Page2`します。</span><span class="sxs-lookup"><span data-stu-id="83095-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="83095-262">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="83095-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="83095-264">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="83095-264">**Configure a filter factory**</span></span>

<span data-ttu-id="83095-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成を適用する、指定されたファクトリ[フィルター](xref:mvc/controllers/filters)すべての Razor ページにします。</span><span class="sxs-lookup"><span data-stu-id="83095-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="83095-266">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="83095-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="83095-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="83095-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="83095-268">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="83095-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="83095-270">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="83095-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="83095-271">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="83095-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="83095-272">MVC フィルターの他の種類を使用するために使用できます。[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)します。</span><span class="sxs-lookup"><span data-stu-id="83095-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="83095-273">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="83095-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="83095-274">ページ フィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) Razor ページに適用されるフィルターです。</span><span class="sxs-lookup"><span data-stu-id="83095-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="83095-275">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="83095-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83095-276">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="83095-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
