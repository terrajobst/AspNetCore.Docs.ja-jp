---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: guardrex
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914977"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="0a325-103">ASP.NET Core での Razor ページのルートとアプリの規則</span><span class="sxs-lookup"><span data-stu-id="0a325-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="0a325-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0a325-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0a325-105">[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0a325-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="0a325-106">個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="0a325-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="0a325-107">ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したり`@page`するには、ページのディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="0a325-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="0a325-108">詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a325-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="0a325-109">ルートセグメントまたはパラメーター名として使用できない予約語があります。</span><span class="sxs-lookup"><span data-stu-id="0a325-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="0a325-110">詳細については[、「ルーティング:予約済みの](xref:fundamentals/routing#reserved-routing-names)ルーティング名。</span><span class="sxs-lookup"><span data-stu-id="0a325-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="0a325-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0a325-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="0a325-112">シナリオ</span><span class="sxs-lookup"><span data-stu-id="0a325-112">Scenario</span></span> | <span data-ttu-id="0a325-113">このサンプルでは、次のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a325-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="0a325-114">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="0a325-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="0a325-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="0a325-115">Conventions.Add</span></span><ul><li><span data-ttu-id="0a325-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0a325-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="0a325-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0a325-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0a325-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="0a325-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="0a325-119">ルート テンプレートとヘッダーをアプリのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="0a325-120">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="0a325-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="0a325-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0a325-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="0a325-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0a325-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="0a325-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="0a325-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="0a325-124">ルート テンプレートをフォルダー内のページおよび単一ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="0a325-125">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="0a325-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="0a325-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0a325-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="0a325-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0a325-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0a325-128">ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</span><span class="sxs-lookup"><span data-stu-id="0a325-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="0a325-129">ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。</span><span class="sxs-lookup"><span data-stu-id="0a325-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="0a325-130">Razor Pages 規則は、 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>拡張<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>メソッドを使用して、 `Startup`クラスのサービスコレクションに対して追加および構成されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="0a325-131">次の規則の例は、このトピックで後述されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-131">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="0a325-132">ルートの順序</span><span class="sxs-lookup"><span data-stu-id="0a325-132">Route order</span></span>

<span data-ttu-id="0a325-133">ルートは、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>処理 (ルート照合) のを指定します。</span><span class="sxs-lookup"><span data-stu-id="0a325-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="0a325-134">注文</span><span class="sxs-lookup"><span data-stu-id="0a325-134">Order</span></span>            | <span data-ttu-id="0a325-135">動作</span><span class="sxs-lookup"><span data-stu-id="0a325-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="0a325-136">-1</span><span class="sxs-lookup"><span data-stu-id="0a325-136">-1</span></span>               | <span data-ttu-id="0a325-137">ルートは、他のルートが処理される前に処理されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="0a325-138">0</span><span class="sxs-lookup"><span data-stu-id="0a325-138">0</span></span>                | <span data-ttu-id="0a325-139">順序が指定されていません (既定値)。</span><span class="sxs-lookup"><span data-stu-id="0a325-139">Order isn't specified (default value).</span></span> <span data-ttu-id="0a325-140">[割り当て`Order`なし`Order = null`] () の`Order`場合、処理のためにルートは 0 (ゼロ) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="0a325-141">1、2、 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="0a325-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="0a325-142">ルートの処理順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a325-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="0a325-143">ルート処理は規約によって確立されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="0a325-144">ルートは順番に処理されます (-1、0、1、 &hellip; 2、n)。</span><span class="sxs-lookup"><span data-stu-id="0a325-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="0a325-145">ルートが同じ`Order`である場合、最も具体的なルートは最初に照合され、それによって特定のルートが小さくなります。</span><span class="sxs-lookup"><span data-stu-id="0a325-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="0a325-146">同じ`Order`と同数のパラメーターを持つルートが要求 URL と一致する場合、ルートは<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="0a325-147">可能な場合は、確立されたルート処理順序に依存しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="0a325-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="0a325-148">通常、ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="0a325-149">要求を正しくルーティングする`Order`ようにルートのプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱して保守が脆弱になることがあります。</span><span class="sxs-lookup"><span data-stu-id="0a325-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="0a325-150">アプリのルーティングスキームを簡略化するためにシークします。</span><span class="sxs-lookup"><span data-stu-id="0a325-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="0a325-151">このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a325-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="0a325-152">ただし、運用環境のアプリではルート`Order`を設定しないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0a325-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="0a325-153">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="0a325-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="0a325-154">MVC トピックのルートの順序に関する情報は、 [「コントローラーアクションへのルーティング」で参照できます。属性ルート](xref:mvc/controllers/routing#ordering-attribute-routes)の順序付け。</span><span class="sxs-lookup"><span data-stu-id="0a325-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="0a325-155">モデルの規則</span><span class="sxs-lookup"><span data-stu-id="0a325-155">Model conventions</span></span>

<span data-ttu-id="0a325-156">の<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>デリゲートを追加して、Razor Pages に適用される[モデル規則](xref:mvc/controllers/application-model#conventions)を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="0a325-157">ルートモデルの規則をすべてのページに追加する</span><span class="sxs-lookup"><span data-stu-id="0a325-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="0a325-158">を<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>作成し、ページルート<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>モデルの構築中<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>に適用されるインスタンスのコレクションにを追加するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a325-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="0a325-159">サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0a325-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="0a325-161">これにより、サンプルアプリで次のルート一致動作が保証されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="0a325-162">の`TheContactPage/{text?}`ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="0a325-163">連絡先ページのルートの既定の`null`順序は (`Order = 0`) であるため、ルートテンプレートの`{globalTemplate?}`前に一致します。</span><span class="sxs-lookup"><span data-stu-id="0a325-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="0a325-164">`{aboutTemplate?}`ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0a325-165">`{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0a325-166">[About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="0a325-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="0a325-167">`{otherPagesTemplate?}`ルートテンプレートは、このトピックの後半で追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0a325-168">`{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0a325-169">*Pages/otherpages*フォルダー内のいずれかのページが`/OtherPages/Page1/RouteDataValue`ルートパラメーター (など) で要求されると、"routedatavalue" は ( `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` )に読み込まれ、`Order`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="0a325-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0a325-170">可能な限り、を設定`Order`しないで`Order = 0`ください。</span><span class="sxs-lookup"><span data-stu-id="0a325-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0a325-171">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="0a325-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0a325-172"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の`Startup.ConfigureServices`サービスコレクションに MVC を追加すると、Razor Pages のオプション ([追加] など) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a325-173">例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a325-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0a325-174">`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="0a325-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="0a325-177">すべてのページにアプリモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="0a325-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="0a325-178">を作成し、ページアプリ<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>モデルの構築中<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>に適用されるインスタンスのコレクションにを追加します。 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions></span><span class="sxs-lookup"><span data-stu-id="0a325-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="0a325-179">この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。</span><span class="sxs-lookup"><span data-stu-id="0a325-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="0a325-180">クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0a325-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="0a325-181">これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="0a325-182">完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="0a325-183">サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0a325-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a325-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="0a325-185">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="0a325-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="0a325-187">すべてのページにハンドラーモデル規則を追加する</span><span class="sxs-lookup"><span data-stu-id="0a325-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="0a325-188">を<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>作成し、ページハンドラー <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>モデルの構築時<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>に適用されるインスタンスのコレクションにを追加するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a325-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0a325-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a325-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="0a325-190">ページ ルート アクション規則</span><span class="sxs-lookup"><span data-stu-id="0a325-190">Page route action conventions</span></span>

<span data-ttu-id="0a325-191">から<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規約を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0a325-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="0a325-192">フォルダールートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="0a325-192">Folder route model convention</span></span>

<span data-ttu-id="0a325-193">を<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>使用して、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>指定<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>したフォルダーの下にあるすべてのページに対してアクションを呼び出すを作成および追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="0a325-194">サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="0a325-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0a325-196">これにより、単一の`{globalTemplate?}`ルート値が指定されて`1`いる場合に、のテンプレート (トピックで前述した) が、最初のルートデータ値の位置に優先されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0a325-197">*Pages/otherpages*フォルダー内`/OtherPages/Page1/RouteDataValue`のページがルートパラメーター値 (など) で要求されている場合、"routedatavalue"`Order = 1`は () `RouteData.Values["globalTemplate"]`に`RouteData.Values["otherPagesTemplate"]` `Order = 2`読み込まれ、`Order`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="0a325-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0a325-198">可能な限り、を設定`Order`しないで`Order = 0`ください。</span><span class="sxs-lookup"><span data-stu-id="0a325-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0a325-199">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="0a325-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0a325-200">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="0a325-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="0a325-203">ページルートモデルの規則</span><span class="sxs-lookup"><span data-stu-id="0a325-203">Page route model convention</span></span>

<span data-ttu-id="0a325-204">を作成し、指定し<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>た名前を持つページ<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>のに対してアクションを呼び出すを追加します。 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*></span><span class="sxs-lookup"><span data-stu-id="0a325-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="0a325-205">サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a325-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="0a325-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0a325-207">これにより、単一の`{globalTemplate?}`ルート値が指定されて`1`いる場合に、のテンプレート (トピックで前述した) が、最初のルートデータ値の位置に優先されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0a325-208">[About] ページが`/About/RouteDataValue`でルートパラメーター値と共に要求された場合、プロパティの`Order`設定`RouteData.Values["globalTemplate"]`により、" `RouteData.Values["aboutTemplate"]` routedatavalue" は (`Order = 1`) に読み込まれ、(`Order = 2`) ではありません。</span><span class="sxs-lookup"><span data-stu-id="0a325-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0a325-209">可能な限り、を設定`Order`しないで`Order = 0`ください。</span><span class="sxs-lookup"><span data-stu-id="0a325-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0a325-210">正しいルートを選択するには、ルーティングに依存します。</span><span class="sxs-lookup"><span data-stu-id="0a325-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0a325-211">`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。</span><span class="sxs-lookup"><span data-stu-id="0a325-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="0a325-214">パラメータートランスフォーマーを使用してページルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0a325-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="0a325-215">ASP.NET Core によって生成されたページルートは、パラメータートランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="0a325-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="0a325-216">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="0a325-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="0a325-217">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="0a325-218">ページ`PageRouteTransformerConvention`ルートモデルの規則では、アプリケーションで自動的に生成されたページルートのフォルダーとファイル名のセグメントにパラメータートランスフォーマーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="0a325-219">たとえば、 *//* //の Razor Pages ファイルでは、ルートはから`/SubscriptionManagement/ViewAll`に`/subscription-management/view-all`書き直されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="0a325-220">`PageRouteTransformerConvention`は、Razor Pages フォルダーとファイル名から取得した、自動的に生成されたページルートのセグメントのみを変換します。</span><span class="sxs-lookup"><span data-stu-id="0a325-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="0a325-221">`@page`ディレクティブを使用して追加されたルートセグメントは変換されません。</span><span class="sxs-lookup"><span data-stu-id="0a325-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="0a325-222">この規則では、によって<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>追加されたルートを変換することもありません。</span><span class="sxs-lookup"><span data-stu-id="0a325-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="0a325-223">は`PageRouteTransformerConvention` 、の`Startup.ConfigureServices`オプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="0a325-224">ページ ルートの構成</span><span class="sxs-lookup"><span data-stu-id="0a325-224">Configure a page route</span></span>

<span data-ttu-id="0a325-225">指定<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>したページパスにあるページへのルートを構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a325-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="0a325-226">そのページに対して生成されたリンクでは、指定したルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="0a325-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="0a325-227">`AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="0a325-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="0a325-228">サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a325-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="0a325-229">[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="0a325-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="0a325-230">サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。</span><span class="sxs-lookup"><span data-stu-id="0a325-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="0a325-231">また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。</span><span class="sxs-lookup"><span data-stu-id="0a325-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="0a325-232">レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="0a325-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="0a325-235">通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="0a325-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="0a325-236">追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="0a325-239">ページ モデル アクション規則</span><span class="sxs-lookup"><span data-stu-id="0a325-239">Page model action conventions</span></span>

<span data-ttu-id="0a325-240">ページモデルを構成するための<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>機能拡張ポイントを提供するように設計された呼び出し規約を実装する既定のページモデルプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="0a325-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="0a325-241">これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="0a325-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="0a325-242">このセクションの例では、サンプルアプリは、応答`AddHeaderAttribute`ヘッダーを適用するクラス<xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>() を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a325-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0a325-243">規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a325-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="0a325-244">**フォルダー アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="0a325-244">**Folder app model convention**</span></span>

<span data-ttu-id="0a325-245">を作成し、指定し<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>たフォルダーの下に<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>あるすべてのページのインスタンスに対してアクションを呼び出すを追加します。 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*></span><span class="sxs-lookup"><span data-stu-id="0a325-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="0a325-246">このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a325-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="0a325-247">`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="0a325-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="0a325-249">**ページ アプリ モデル規則**</span><span class="sxs-lookup"><span data-stu-id="0a325-249">**Page app model convention**</span></span>

<span data-ttu-id="0a325-250">を作成し、指定し<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>た名前を持つページ<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>のに対してアクションを呼び出すを追加します。 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*></span><span class="sxs-lookup"><span data-stu-id="0a325-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="0a325-251">サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a325-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="0a325-252">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="0a325-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="0a325-254">**フィルターの構成**</span><span class="sxs-lookup"><span data-stu-id="0a325-254">**Configure a filter**</span></span>

<span data-ttu-id="0a325-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>適用するように指定されたフィルターを構成します。</span><span class="sxs-lookup"><span data-stu-id="0a325-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="0a325-256">フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="0a325-257">ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="0a325-258">条件を満たすと、ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="0a325-259">満たさない場合は、`EmptyFilter` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="0a325-260">`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。</span><span class="sxs-lookup"><span data-stu-id="0a325-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="0a325-261">アクションフィルターは Razor Pages によって無視さ`EmptyFilter`れるため、パスにが含まれ`OtherPages/Page2`ていない場合、は意図したとおりには効果がありません。</span><span class="sxs-lookup"><span data-stu-id="0a325-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="0a325-262">`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="0a325-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="0a325-264">**フィルター ファクトリの構成**</span><span class="sxs-lookup"><span data-stu-id="0a325-264">**Configure a filter factory**</span></span>

<span data-ttu-id="0a325-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成します。</span><span class="sxs-lookup"><span data-stu-id="0a325-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="0a325-266">サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。</span><span class="sxs-lookup"><span data-stu-id="0a325-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="0a325-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a325-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0a325-268">`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="0a325-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="0a325-270">MVC フィルターとページ フィルター (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="0a325-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="0a325-271">Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="0a325-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="0a325-272">他の種類の MVC フィルターを使用することもできます。[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="0a325-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="0a325-273">詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a325-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="0a325-274">ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。</span><span class="sxs-lookup"><span data-stu-id="0a325-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="0a325-275">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a325-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a325-276">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0a325-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
