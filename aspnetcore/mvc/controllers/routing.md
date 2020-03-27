---
title: ASP.NET Core でのコントローラー アクションへのルーティング
author: rick-anderson
description: ASP.NET Core MVC でルーティング ミドルウェアを使って、受信した要求の URL を照合し、アクションにマップする方法について説明します。
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c1c0d978714718af1de0f627e50a54f66ed391ed
ms.sourcegitcommit: 4b166b49ec557a03f99f872dd069ca5e56faa524
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80362645"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="ccfe7-103">ASP.NET Core でのコントローラー アクションへのルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="ccfe7-104">[ライアン Nowak](https://github.com/rynowak)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ccfe7-105">ASP.NET Core コントローラーは、ルーティング[ミドルウェア](xref:fundamentals/middleware/index)を使用して受信要求の url を照合し、[アクション](#action)にマップします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="ccfe7-106">ルートテンプレート:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-106">Routes templates:</span></span>

* <span data-ttu-id="ccfe7-107">はスタートアップコードまたは属性で定義されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="ccfe7-108">URL パスと[アクション](#action)の照合方法を記述します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="ccfe7-109">リンクの Url を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="ccfe7-110">生成されたリンクは通常、応答で返されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="ccfe7-111">アクションは、[慣例的にルーティング](#cr)されるか、または[属性ルーティング](#ar)されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="ccfe7-112">コントローラーまたは[アクション](#action)にルートを配置すると、ルートが属性ルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="ccfe7-113">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ccfe7-114">このドキュメント:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-114">This document:</span></span>

* <span data-ttu-id="ccfe7-115">MVC とルーティングの間の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="ccfe7-116">一般的な MVC アプリでルーティング機能を利用する方法。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="ccfe7-117">次の両方について説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-117">Covers both:</span></span>
    * <span data-ttu-id="ccfe7-118">通常、[従来のルーティング](#cr)では、コントローラーとビューが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="ccfe7-119">REST Api で使用される*属性ルーティング*。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="ccfe7-120">REST Api のルーティングに主に関心がある場合は、「 [Rest api の属性ルーティング](#ar)」セクションに進んでください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="ccfe7-121">詳細については、「[ルーティング](xref:fundamentals/routing)の詳細」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="ccfe7-122">ASP.NET Core 3.0 で追加された既定のルーティングシステムを指します (エンドポイントルーティングと呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="ccfe7-123">以前のバージョンのルーティングでは、互換性のためにコントローラーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="ccfe7-124">手順については、 [2.2-3.0 移行ガイド](xref:migration/22-to-30)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="ccfe7-125">レガシルーティングシステムのリファレンス資料については、[このドキュメントの2.2 バージョン](xref:mvc/controllers/routing?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="ccfe7-126">従来のルートの設定</span><span class="sxs-lookup"><span data-stu-id="ccfe7-126">Set up conventional route</span></span>

<span data-ttu-id="ccfe7-127">通常、`Startup.Configure` は、通常の[ルーティング](#crd)を使用するときに、次のようなコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="ccfe7-128"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>の呼び出しの内部では、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> を使用して1つのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="ccfe7-129">1つのルートには `default` route という名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-129">The single route is named `default` route.</span></span> <span data-ttu-id="ccfe7-130">コントローラーとビューを使用するほとんどのアプリでは、`default` ルートと同様のルートテンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="ccfe7-131">REST Api では、[属性ルーティング](#ar)を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="ccfe7-132">ルートテンプレート `"{controller=Home}/{action=Index}/{id?}"`:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="ccfe7-133">次のような URL パスと一致し `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="ccfe7-134">パスをトークン化して `{ controller = Products, action = Details, id = 5 }` ルート値を抽出します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ccfe7-135">アプリに `ProductsController` という名前のコントローラーと `Details` アクションがある場合、ルート値を抽出した結果が一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

 <span data-ttu-id="ccfe7-136">[Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-136">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

  * <span data-ttu-id="ccfe7-137">`/Products/Details/5` モデルは、`id = 5` の値をバインドして、`id` パラメーターを `5`に設定します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-137">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="ccfe7-138">詳細については、「[モデルバインド](xref:mvc/models/model-binding)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-138">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="ccfe7-139">`{controller=Home}` は、`Home` を既定の `controller`として定義します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-139">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="ccfe7-140">`{action=Index}` は、`Index` を既定の `action`として定義します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-140">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="ccfe7-141">`{id?}` の `?` 文字は、`id` を省略可能として定義します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-141">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="ccfe7-142">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-142">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ccfe7-143">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-143">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="ccfe7-144">URL パス `/`と一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-144">Matches the URL path `/`.</span></span>
* <span data-ttu-id="ccfe7-145">`{ controller = Home, action = Index }`ルート値を生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-145">Produces the route values `{ controller = Home, action = Index }`.</span></span>
* <span data-ttu-id="ccfe7-146">[Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-146">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

<span data-ttu-id="ccfe7-147">`controller` と `action` の値によって、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-147">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="ccfe7-148">URL パスに対応するセグメントがないため、`id` は値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-148">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ccfe7-149">`/` は、`HomeController` と `Index` アクションが存在する場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-149">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ccfe7-150">前のコントローラー定義とルートテンプレートを使用して、次の URL パスに対して `HomeController.Index` アクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-150">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="ccfe7-151">`/` URL パスでは、ルートテンプレートの既定の `Home` コントローラーと `Index` アクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-151">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="ccfe7-152">ルートテンプレートの既定 `Index` アクションを使用 `/Home` URL パス。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-152">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="ccfe7-153">便利なメソッド <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-153">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="ccfe7-154">もの</span><span class="sxs-lookup"><span data-stu-id="ccfe7-154">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ccfe7-155">ルーティングは、<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> と <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ミドルウェアを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-155">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="ccfe7-156">コントローラーを使用するには:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-156">To use controllers:</span></span>

* <span data-ttu-id="ccfe7-157">[属性ルーティング](#ar)コントローラーをマップするには `UseEndpoints` 内で <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-157">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="ccfe7-158"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> または <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>を呼び出して、[従来のルーティング](#cr)コントローラーをマップします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-158">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ccfe7-159">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-159">Conventional routing</span></span>

<span data-ttu-id="ccfe7-160">従来のルーティングは、コントローラーとビューで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-160">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="ccfe7-161">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-161">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="ccfe7-162">これは、"*規則ルーティング*" の例です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-162">is an example of a *conventional routing*.</span></span> <span data-ttu-id="ccfe7-163">これは、URL パスの*規則*を確立するため、*従来のルーティング*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-163">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ccfe7-164">最初のパスセグメントである `{controller=Home}`は、コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-164">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="ccfe7-165">2番目のセグメントである `{action=Index}`は、[アクション](#action)名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-165">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="ccfe7-166">3番目のセグメント `{id?}` は、オプションの `id`に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-166">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="ccfe7-167">`{id?}` の `?` によってオプションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-167">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="ccfe7-168">`id` は、モデルエンティティにマップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-168">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="ccfe7-169">この `default` ルートを使用して、URL パスを次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-169">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="ccfe7-170">`/Products/List` `ProductsController.List` アクションにマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-170">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="ccfe7-171">`/Blog/Article/17` は `BlogController.Article` にマップされ、通常、モデルは `id` パラメーターを17にバインドします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-171">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="ccfe7-172">このマッピングは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-172">This mapping:</span></span>

* <span data-ttu-id="ccfe7-173">は、コントローラー名と[アクション](#action)名**のみ**に基づいています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-173">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="ccfe7-174">は、名前空間、ソースファイルの場所、またはメソッドのパラメーターに基づいていません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-174">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="ccfe7-175">既定のルートで従来のルーティングを使用すると、各アクションの新しい URL パターンを使用しなくてもアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-175">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="ccfe7-176">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)スタイルのアクションを使用するアプリの場合、コントローラー間で url の一貫性を確保します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-176">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="ccfe7-177">コードを簡略化するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-177">Helps simplify the code.</span></span>
* <span data-ttu-id="ccfe7-178">UI の予測可能性を向上させます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-178">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ccfe7-179">前のコードの `id` は、ルートテンプレートによってオプションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-179">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="ccfe7-180">アクションは、URL の一部として指定された省略可能な ID なしで実行できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-180">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="ccfe7-181">一般に、URL から`id` を省略すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-181">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="ccfe7-182">`id` は、モデルバインドによって `0` するように設定されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-182">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="ccfe7-183">`id == 0`一致するエンティティがデータベースに見つかりません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-183">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="ccfe7-184">[属性ルーティング](#ar)を使用すると、他のアクションではなく、一部のアクションに必要な ID を作成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-184">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ccfe7-185">慣例により、ドキュメントには、正しい使用状況で表示される可能性がある `id` のような省略可能なパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-185">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="ccfe7-186">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-186">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="ccfe7-187">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-187">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="ccfe7-188">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-188">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="ccfe7-189">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-189">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="ccfe7-190">は、多くの web UI アプリに必要な唯一のルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-190">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="ccfe7-191">大規模な web UI アプリの場合は、必要な場合は、[領域](#areas)を使用する別のルートがあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-191">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="ccfe7-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> と <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="ccfe7-193">呼び出された順序に基づいて、エンドポイントに**注文**値を自動的に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-193">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="ccfe7-194">ASP.NET Core 3.0 以降でのエンドポイントのルーティング:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-194">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="ccfe7-195">ルートの概念はありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-195">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="ccfe7-196">は、拡張性の実行に対する順序の保証を提供しません。すべてのエンドポイントが一度に処理されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-196">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="ccfe7-197">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-197">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="ccfe7-198">[属性ルーティング](#ar)については、このドキュメントで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-198">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="ccfe7-199">複数の従来のルート</span><span class="sxs-lookup"><span data-stu-id="ccfe7-199">Multiple conventional routes</span></span>

<span data-ttu-id="ccfe7-200"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> と <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>に対する呼び出しをさらに追加することで、複数の[従来のルート](#cr)を `UseEndpoints` 内部に追加できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-200">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="ccfe7-201">これにより、複数の規則を定義したり、次のような特定の[アクション](#action)専用の通常のルートを追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-201">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="ccfe7-202">前のコードの `blog` ルートは、**専用の従来のルート**です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-202">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="ccfe7-203">これは、次の理由で専用の従来のルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-203">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="ccfe7-204">従来の[ルーティング](#cr)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-204">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="ccfe7-205">これは特定の[アクション](#action)専用です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-205">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="ccfe7-206">`controller` と `action` がパラメーターとしてルートテンプレート `"blog/{*article}"` に表示されないため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-206">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="ccfe7-207">`{ controller = "Blog", action = "Article" }`既定値のみを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-207">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="ccfe7-208">このルートは、常にアクション `BlogController.Article`にマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-208">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ccfe7-209">`/Blog`、`/Blog/Article`、および `/Blog/{any-string}` は、ブログルートに一致する唯一の URL パスです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-209">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="ccfe7-210">前の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-210">The preceding example:</span></span>

* <span data-ttu-id="ccfe7-211">`blog` ルートは最初に追加されるため、`default` ルートよりも優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-211">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="ccfe7-212">とは、URL の一部としてアーティクル名を持つ一般的な[スラグ](https://developer.mozilla.org/docs/Glossary/Slug)スタイルのルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-212">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="ccfe7-213">ASP.NET Core 3.0 以降では、ルーティングは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-213">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="ccfe7-214">*ルート*と呼ばれる概念を定義します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-214">Define a concept called a *route*.</span></span> <span data-ttu-id="ccfe7-215">`UseRouting` は、ミドルウェアパイプラインにルート一致を追加します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-215">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="ccfe7-216">`UseRouting` ミドルウェアは、アプリで定義されているエンドポイントのセットを調べ、要求に基づいて最適なエンドポイント一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-216">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="ccfe7-217"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> や <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>などの機能拡張の実行順序についての保証を提供します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-217">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="ccfe7-218">ルーティングのリファレンス資料については、「[ルーティング](xref:fundamentals/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-218">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="ccfe7-219">従来のルーティング順序</span><span class="sxs-lookup"><span data-stu-id="ccfe7-219">Conventional routing order</span></span>

<span data-ttu-id="ccfe7-220">従来のルーティングは、アプリによって定義されたアクションとコントローラーの組み合わせにのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-220">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="ccfe7-221">これは、通常のルートが重複するケースを簡略化することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-221">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="ccfe7-222"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>、および <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> を使用してルートを追加すると、呼び出された順序に基づいて、そのエンドポイントに注文の値が自動的に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-222">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="ccfe7-223">以前に表示されたルートからの一致は、優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-223">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="ccfe7-224">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-224">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ccfe7-225">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-225">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="ccfe7-226">`{*article}` のようなすべてのルートパラメーターをキャッチした[専用](#dcr)のルートでは、ルートの[最長](xref:fundamentals/routing#greedy)長を指定できます。つまり、他のルートと一致するように意図した url と一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-226">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ccfe7-227">最短一致の一致を防ぐために、ルートテーブルの中で最長一致のルートを指定します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-227">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="ccfe7-228">あいまいなアクションの解決</span><span class="sxs-lookup"><span data-stu-id="ccfe7-228">Resolving ambiguous actions</span></span>

<span data-ttu-id="ccfe7-229">ルーティングを通じて2つのエンドポイントが一致する場合、ルーティングでは次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-229">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="ccfe7-230">最適な候補を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-230">Choose the best candidate.</span></span>
* <span data-ttu-id="ccfe7-231">例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-231">Throw an exception.</span></span>

<span data-ttu-id="ccfe7-232">例 :</span><span class="sxs-lookup"><span data-stu-id="ccfe7-232">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="ccfe7-233">前のコントローラーは、次の2つのアクションに一致するアクションを定義します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-233">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="ccfe7-234">URL パス `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-234">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="ccfe7-235">データ `{ controller = Products33, action = Edit, id = 17 }`をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-235">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="ccfe7-236">これは、MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-236">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="ccfe7-237">`Edit(int)` には、製品を編集するためのフォームが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-237">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="ccfe7-238">`Edit(int, Product)` は、ポストされたフォームを処理します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-238">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="ccfe7-239">正しいルートを解決するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-239">To resolve the correct route:</span></span>

* <span data-ttu-id="ccfe7-240">要求が HTTP `POST`の場合は `Edit(int, Product)` が選択されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-240">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="ccfe7-241">[HTTP 動詞](#verb)が他のものである場合は `Edit(int)` が選択されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-241">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="ccfe7-242">`Edit(int)` は、一般に `GET`経由で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-242">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="ccfe7-243"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、`[HttpPost]`は、要求の HTTP メソッドに基づいて選択できるように、ルーティングのために用意されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-243">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="ccfe7-244">`HttpPostAttribute` によって、`Edit(int)`よりも `Edit(int, Product)` の方が適しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-244">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="ccfe7-245">`HttpPostAttribute`のような属性の役割を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-245">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="ccfe7-246">同様の属性は、他の[HTTP 動詞](#verb)に対して定義されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-246">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="ccfe7-247">[従来のルーティング](#cr)では、表示フォーム、送信フォームワークフローの一部である場合、アクションは同じアクション名を使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-247">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="ccfe7-248">例については、「 [2 つの編集アクションメソッドの確認](xref:tutorials/first-mvc-app/controller-methods-views#get-post)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-248">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="ccfe7-249">ルーティングで最適な候補を選択できない場合は、一致する複数のエンドポイントを一覧表示する <xref:System.Reflection.AmbiguousMatchException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-249">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="ccfe7-250">従来のルート名</span><span class="sxs-lookup"><span data-stu-id="ccfe7-250">Conventional route names</span></span>

<span data-ttu-id="ccfe7-251">次の例の文字列 `"blog"` と `"default"` は、通常のルート名です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-251">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ccfe7-252">ルート名によって、ルートに論理名が指定されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-252">The route names give the route a logical name.</span></span> <span data-ttu-id="ccfe7-253">名前付きルートは、URL の生成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-253">The named route can be used for URL generation.</span></span> <span data-ttu-id="ccfe7-254">ルートの順序によって URL の生成が複雑になる可能性がある場合、名前付きルートを使用すると、URL の作成が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-254">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ccfe7-255">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-255">Route names must be unique application wide.</span></span>

<span data-ttu-id="ccfe7-256">ルート名:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-256">Route names:</span></span>

* <span data-ttu-id="ccfe7-257">URL の照合や要求の処理には影響しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-257">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="ccfe7-258">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-258">Are used only for URL generation.</span></span>

<span data-ttu-id="ccfe7-259">ルート名の概念は、ルーティングで[IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)として表されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-259">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="ccfe7-260">**ルート名**と**エンドポイント名**の用語:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-260">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="ccfe7-261">交換可能です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-261">Are interchangeable.</span></span>
* <span data-ttu-id="ccfe7-262">ドキュメントとコードで使用されるものは、記述されている API によって異なります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-262">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="ccfe7-263">REST Api の属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-263">Attribute routing for REST APIs</span></span>

<span data-ttu-id="ccfe7-264">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が[HTTP 動詞](#verb)によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-264">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="ccfe7-265">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-265">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ccfe7-266">次の `StartUp.Configure` コードは、REST API の一般的なもので、次のサンプルで使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-266">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="ccfe7-267">前のコードでは、属性ルーティングコントローラーをマップするために、`UseEndpoints` 内部で <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-267">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="ccfe7-268">たとえば、行が次のように表示されているとします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-268">In the following example:</span></span>

* <span data-ttu-id="ccfe7-269">上記の `Configure` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-269">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="ccfe7-270">`HomeController` は、既定の従来のルート `{controller=Home}/{action=Index}/{id?}` 一致と同様の Url のセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-270">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="ccfe7-271">`HomeController.Index` アクションは、URL パス `/`、`/Home`、`/Home/Index`、または `/Home/Index/3`に対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-271">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="ccfe7-272">この例では、属性ルーティングと[従来のルーティング](#cr)の主なプログラミングの違いについて取り上げます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-272">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="ccfe7-273">属性ルーティングでは、ルートを指定するためにより多くの入力が必要です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-273">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="ccfe7-274">従来の既定のルートは、ルートをより簡潔に処理します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-274">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ccfe7-275">ただし、属性ルーティングでは、各[アクション](#action)に適用されるルートテンプレートを正確に制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-275">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="ccfe7-276">属性ルーティングでは、コントローラー名とアクション名は、アクションが一致する役割を果たし**ません**。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-276">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="ccfe7-277">次の例は、前の例と同じ Url に一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-277">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ccfe7-278">次のコードでは、`action` と `controller`にトークン置換を使用しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-278">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="ccfe7-279">次のコードは、コントローラーに `[Route("[controller]/[action]")]` を適用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-279">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="ccfe7-280">前のコードでは、`Index` メソッドテンプレートをルートテンプレートに `/` または `~/` する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-280">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="ccfe7-281">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-281">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="ccfe7-282">ルートテンプレートの選択の詳細については、[ルートテンプレートの優先順位](xref:fundamentals/routing#rtp)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-282">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="ccfe7-283">ルーティングの予約名</span><span class="sxs-lookup"><span data-stu-id="ccfe7-283">Reserved routing names</span></span>

<span data-ttu-id="ccfe7-284">コントローラーまたは Razor Pages を使用する場合は、次のキーワードが予約されたルートパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-284">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="ccfe7-285">属性ルーティングを使用してルートパラメーターとして `page` を使用することは、一般的なエラーです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-285">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="ccfe7-286">その結果、URL の生成によって一貫性のない動作がわかりにくくなります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-286">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="ccfe7-287">特別なパラメーター名は、url 生成操作が Razor ページとコントローラーのどちらを参照するかを決定するために、URL 生成によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-287">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="ccfe7-288">HTTP 動詞テンプレート</span><span class="sxs-lookup"><span data-stu-id="ccfe7-288">HTTP verb templates</span></span>

<span data-ttu-id="ccfe7-289">ASP.NET Core には、次の HTTP 動詞テンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-289">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="ccfe7-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="ccfe7-291">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-291">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="ccfe7-292">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-292">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="ccfe7-293">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-293">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="ccfe7-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="ccfe7-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="ccfe7-296">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="ccfe7-296">Route templates</span></span>

<span data-ttu-id="ccfe7-297">ASP.NET Core には、次のルートテンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-297">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="ccfe7-298">すべての[HTTP 動詞テンプレート](#verb)はルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-298">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="ccfe7-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="ccfe7-300">Http 動詞属性を使用した属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-300">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="ccfe7-301">次のコントローラーを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-301">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="ccfe7-302">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-302">In the preceding code:</span></span>

* <span data-ttu-id="ccfe7-303">各アクションには `[HttpGet]` 属性が含まれています。これにより、一致する HTTP GET 要求のみが制限されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-303">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="ccfe7-304">`GetProduct` アクションには `"{id}"` テンプレートが含まれているため、`id` はコントローラーの `"api/[controller]"` テンプレートに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-304">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="ccfe7-305">メソッドテンプレートは `"api/[controller]/"{id}""`。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-305">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="ccfe7-306">したがって、このアクションでは、`/api/test2/xyz`、`/api/test2/123`、`/api/test2/{any string}`などの形式の GET 要求のみが一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-306">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="ccfe7-307">`GetIntProduct` アクションには、`"int/{id:int}")` テンプレートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-307">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="ccfe7-308">テンプレートの `:int` の部分は、`id` ルート値を整数に変換できる文字列に制限します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-308">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="ccfe7-309">`/api/test2/int/abc`する GET 要求:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-309">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="ccfe7-310">このアクションと一致しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-310">Doesn't match this action.</span></span>
  * <span data-ttu-id="ccfe7-311">[404 が見つからないこと](https://developer.mozilla.org/docs/Web/HTTP/Status/404)を示すエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-311">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="ccfe7-312">`GetInt2Product` アクションには、テンプレート内の `{id}` が含まれていますが、整数に変換できる値に `id` を制限していません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-312">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="ccfe7-313">`/api/test2/int2/abc`する GET 要求:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-313">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="ccfe7-314">このルートと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-314">Matches this route.</span></span>
  * <span data-ttu-id="ccfe7-315">モデルバインドが `abc` を整数に変換できません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-315">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="ccfe7-316">メソッドの `id` パラメーターは integer です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-316">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="ccfe7-317">モデルバインドが`abc` を整数に変換できなかったため、 [400 の無効な要求](https://developer.mozilla.org/docs/Web/HTTP/Status/400)が返されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-317">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="ccfe7-318">属性ルーティングでは、<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、<xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>、<xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>などの <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> 属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-318">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="ccfe7-319">すべての[HTTP 動詞](#verb)属性は、ルートテンプレートを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-319">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="ccfe7-320">次の例は、同じルートテンプレートに一致する2つのアクションを示しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-320">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="ccfe7-321">`/products3`URL パスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-321">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="ccfe7-322">`MyProductsController.ListProducts` アクションは、 [HTTP 動詞](#verb)が `GET`ときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-322">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="ccfe7-323">`MyProductsController.CreateProduct` アクションは、 [HTTP 動詞](#verb)が `POST`ときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-323">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="ccfe7-324">REST API をビルドする場合、アクションはすべての HTTP メソッドを受け入れるため、アクションメソッドで `[Route(...)]` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-324">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="ccfe7-325">API がサポートする内容については、より具体的な[HTTP 動詞属性](#verb)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-325">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="ccfe7-326">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-326">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ccfe7-327">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が HTTP 動詞によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-327">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="ccfe7-328">これは、同じ論理リソースに対する GET や POST などの多くの操作で同じ URL が使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-328">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="ccfe7-329">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-329">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="ccfe7-330">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-330">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ccfe7-331">次の例では、URL パスの一部として `id` が必要です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-331">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ccfe7-332">`Products2ApiController.GetProduct(int)` アクション:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-332">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="ccfe7-333">は、のような URL パスを使用して実行され `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-333">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="ccfe7-334">は、URL パス `/products2`では実行されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-334">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="ccfe7-335">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-335">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="ccfe7-336">詳細については、「[利用属性を使用したサポートされる要求のコンテンツの種類の定義](xref:web-api/index#consumes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-336">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="ccfe7-337">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-337">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="ccfe7-338">`[ApiController]`の詳細については、「 [ApiController 属性](xref:web-api/index##apicontroller-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-338">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="ccfe7-339">ルート名</span><span class="sxs-lookup"><span data-stu-id="ccfe7-339">Route name</span></span>

<span data-ttu-id="ccfe7-340">次のコードでは、`Products_List`のルート名を定義しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-340">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ccfe7-341">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-341">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ccfe7-342">ルート名:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-342">Route names:</span></span>

* <span data-ttu-id="ccfe7-343">ルーティングの URL 照合動作には影響しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-343">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="ccfe7-344">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-344">Are only used for URL generation.</span></span>

<span data-ttu-id="ccfe7-345">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-345">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ccfe7-346">上記のコードと従来の既定のルートを比較します。これにより、`id` のパラメーターが省略可能な (`{id?}`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-346">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ccfe7-347">Api を正確に指定する機能には、`/products` と `/products/5` を異なるアクションにディスパッチできるようにするなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-347">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="ccfe7-348">属性ルートの結合</span><span class="sxs-lookup"><span data-stu-id="ccfe7-348">Combining attribute routes</span></span>

<span data-ttu-id="ccfe7-349">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-349">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ccfe7-350">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-350">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ccfe7-351">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-351">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="ccfe7-352">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-352">In the preceding example:</span></span>

* <span data-ttu-id="ccfe7-353">`/products` URL パスを一致させることができ `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-353">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="ccfe7-354">URL パス `/products/5` は `ProductsApi.GetProduct(int)`と一致できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-354">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="ccfe7-355">これらのアクションはどちらも、`[HttpGet]` 属性でマークされているので、HTTP `GET` にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-355">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="ccfe7-356">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-356">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ccfe7-357">次の例は、既定のルートに似た URL パスのセットに一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-357">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="ccfe7-358">次の表では、上記のコードの `[Route]` の属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-358">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="ccfe7-359">属性</span><span class="sxs-lookup"><span data-stu-id="ccfe7-359">Attribute</span></span>               | <span data-ttu-id="ccfe7-360">`[Route("Home")]` と結合します</span><span class="sxs-lookup"><span data-stu-id="ccfe7-360">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="ccfe7-361">ルートテンプレートを定義します</span><span class="sxs-lookup"><span data-stu-id="ccfe7-361">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="ccfe7-362">はい</span><span class="sxs-lookup"><span data-stu-id="ccfe7-362">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="ccfe7-363">はい</span><span class="sxs-lookup"><span data-stu-id="ccfe7-363">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="ccfe7-364">**No**</span><span class="sxs-lookup"><span data-stu-id="ccfe7-364">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="ccfe7-365">はい</span><span class="sxs-lookup"><span data-stu-id="ccfe7-365">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="ccfe7-366">属性のルートの順序</span><span class="sxs-lookup"><span data-stu-id="ccfe7-366">Attribute route order</span></span>

<span data-ttu-id="ccfe7-367">ルーティングによってツリーが構築され、すべてのエンドポイントが同時に一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-367">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="ccfe7-368">ルートエントリは、最適な順序で配置されているかのように動作します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-368">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="ccfe7-369">最も具体的なルートは、より一般的なルートの前に実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-369">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ccfe7-370">たとえば、`blog/search/{topic}` のような属性ルートは、`blog/{*article}`のような属性ルートよりも具体的です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-370">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="ccfe7-371">既定では、`blog/search/{topic}` ルートの方が優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-371">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="ccfe7-372">開発者は、[従来のルーティング](#cr)を使用して、ルートを目的の順序で配置します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-372">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ccfe7-373">属性ルートでは、<xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> プロパティを使用して注文を構成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-373">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="ccfe7-374">フレームワークに用意されているすべての[ルート属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)には、`Order` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-374">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="ccfe7-375">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-375">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ccfe7-376">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-376">The default order is `0`.</span></span> <span data-ttu-id="ccfe7-377">`Order = -1` を使用してルートを設定すると、順序を設定していないルートの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-377">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="ccfe7-378">`Order = 1` を使用したルートの設定は、既定のルートの順序の後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-378">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="ccfe7-379">`Order`によっては**避けるよう**にしてください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-379">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="ccfe7-380">アプリの URL 空間で、明示的な順序の値を正しくルーティングする必要がある場合は、クライアントにも混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-380">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ccfe7-381">一般に、属性ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-381">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ccfe7-382">URL の生成に使用される既定の順序が機能していない場合、通常は、`Order` プロパティを適用するよりも、ルート名をオーバーライドとして使用する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-382">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="ccfe7-383">次の2つのコントローラーを考えてみます。 `/home`に一致するルートが定義されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-383">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ccfe7-384">前のコードで `/home` を要求すると、次のような例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-384">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="ccfe7-385">ルート属性のいずれかに `Order` を追加すると、あいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-385">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="ccfe7-386">上記のコードでは、`/home` が `HomeController.Index` エンドポイントを実行します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-386">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="ccfe7-387">`MyDemoController.MyIndex`にアクセスするには、`/home/MyIndex`を要求します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-387">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="ccfe7-388">**注**:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-388">**Note**:</span></span>

* <span data-ttu-id="ccfe7-389">上記のコードは、ルーティング設計の一例または不十分です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-389">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="ccfe7-390">これは、`Order` プロパティを示すために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-390">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="ccfe7-391">`Order` プロパティはあいまいさを解決するだけで、そのテンプレートは一致しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-391">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="ccfe7-392">`[Route("Home")]` テンプレートを削除することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-392">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="ccfe7-393">Razor Pages を使用したルートの順序については[、Razor Pages ルートとアプリの規則](xref:razor-pages/razor-pages-conventions#route-order)に関する説明を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-393">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="ccfe7-394">場合によっては、あいまいなルートで HTTP 500 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-394">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="ccfe7-395">[ログ](xref:fundamentals/logging/index)を使用して、`AmbiguousMatchException`の原因となったエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-395">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ccfe7-396">ルートテンプレートでのトークンの置換 [controller]、[action]、[area]</span><span class="sxs-lookup"><span data-stu-id="ccfe7-396">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="ccfe7-397">便宜上、属性ルートは、次のいずれかのトークンを囲むことによって、予約ルートパラメーターのトークン置換をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-397">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="ccfe7-398">角かっこ: `[]`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-398">Square braces: `[]`</span></span>
* <span data-ttu-id="ccfe7-399">中かっこ: `{}`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-399">Curly braces: `{}`</span></span>

<span data-ttu-id="ccfe7-400">トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、領域名、およびコントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-400">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="ccfe7-401">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-401">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="ccfe7-402">一致する `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-402">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="ccfe7-403">一致する `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-403">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="ccfe7-404">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-404">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ccfe7-405">前の例では、次のコードと同じ動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-405">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="ccfe7-406">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-406">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ccfe7-407">これは、トークンの置換と組み合わせた強力な機能です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-407">This is powerful combined with token replacement.</span></span> <span data-ttu-id="ccfe7-408">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-408">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="ccfe7-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`によって、各アクションの一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="ccfe7-410">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-410">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="ccfe7-411">各アクションの一意のルート名を生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-411">generates a unique route name for each action.</span></span>

<span data-ttu-id="ccfe7-412">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-412">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ccfe7-413">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="ccfe7-413">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ccfe7-414">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-414">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ccfe7-415">パラメーター トランスフォーマーは <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-415">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="ccfe7-416">たとえば、カスタムの `SlugifyParameterTransformer` パラメータートランスフォーマーは、`SubscriptionManagement` ルート値を `subscription-management`に変更します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-416">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="ccfe7-417"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-417">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="ccfe7-418">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-418">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ccfe7-419">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-419">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="ccfe7-420">前の `ListAll` メソッドは `/subscription-management/list-all`と一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-420">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="ccfe7-421">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-421">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="ccfe7-422">スラグの定義については、[スラグの MDN web ドキュメント](https://developer.mozilla.org/docs/Glossary/Slug)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-422">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="ccfe7-423">複数の属性ルート</span><span class="sxs-lookup"><span data-stu-id="ccfe7-423">Multiple attribute routes</span></span>

<span data-ttu-id="ccfe7-424">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-424">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ccfe7-425">この最も一般的な使用方法は、次の例に示すように、既定のルートの動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-425">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="ccfe7-426">コントローラーに複数のルート属性を配置することは、それぞれがアクションメソッドの各ルート属性と結合することを意味します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-426">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="ccfe7-427">すべての[HTTP 動詞](#verb)ルート制約は、`IActionConstraint`を実装します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-427">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="ccfe7-428"><xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> を実装する複数のルート属性がアクションに配置されている場合:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-428">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="ccfe7-429">各アクション制約は、コントローラーに適用されたルートテンプレートと結合されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-429">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="ccfe7-430">アクションで複数のルートを使用すると便利で強力な場合があります。アプリの URL 空間の基本を明確に定義しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-430">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="ccfe7-431">既存のクライアントをサポートするために必要な場合に**のみ**、アクションで複数のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-431">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ccfe7-432">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="ccfe7-432">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ccfe7-433">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-433">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="ccfe7-434">上記のコードでは、`[HttpPost("product/{id:int}")]` はルート制約を適用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-434">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="ccfe7-435">`ProductsController.ShowProduct` アクションは、`/product/3`のような URL パスによってのみ照合されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-435">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="ccfe7-436">ルートテンプレートの部分は、そのセグメントを整数のみに制限 `{id:int}` ます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-436">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="ccfe7-437">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-437">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ccfe7-438">IRouteTemplateProvider を使用したカスタムルート属性</span><span class="sxs-lookup"><span data-stu-id="ccfe7-438">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="ccfe7-439">すべての[ルート属性](#rt)は <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>を実装します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-439">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="ccfe7-440">ASP.NET Core ランタイム:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-440">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="ccfe7-441">アプリの起動時に、コントローラークラスとアクションメソッドの属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-441">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="ccfe7-442">`IRouteTemplateProvider` を実装する属性を使用して、ルートの初期セットを構築します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-442">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ccfe7-443">`IRouteTemplateProvider` を実装して、カスタムルート属性を定義します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-443">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="ccfe7-444">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-444">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="ccfe7-445">前の `Get` メソッドは `Order = 2, Template = api/MyTestApi`を返します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-445">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ccfe7-446">アプリケーションモデルを使用して属性ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="ccfe7-446">Use application model to customize attribute routes</span></span>

<span data-ttu-id="ccfe7-447">アプリケーションモデル:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-447">The application model:</span></span>

* <span data-ttu-id="ccfe7-448">は、起動時に作成されるオブジェクトモデルです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-448">Is an object model created at startup.</span></span>
* <span data-ttu-id="ccfe7-449">アプリでアクションをルーティングおよび実行するために ASP.NET Core によって使用されるすべてのメタデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-449">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="ccfe7-450">アプリケーションモデルには、ルート属性から収集されたすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-450">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="ccfe7-451">ルート属性からのデータは、`IRouteTemplateProvider` の実装によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-451">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="ccfe7-452">規定</span><span class="sxs-lookup"><span data-stu-id="ccfe7-452">Conventions:</span></span>

* <span data-ttu-id="ccfe7-453">アプリケーションモデルを変更して、ルーティングの動作をカスタマイズするために、を記述できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-453">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="ccfe7-454">アプリの起動時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-454">Are read at app startup.</span></span>

<span data-ttu-id="ccfe7-455">このセクションでは、アプリケーションモデルを使用してルーティングをカスタマイズする基本的な例を示します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-455">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="ccfe7-456">次のコードでは、ルートがプロジェクトのフォルダー構造とほぼ同じになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-456">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="ccfe7-457">次のコードは、属性がルーティングされているコントローラーに `namespace` 規則が適用されないようにします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-457">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="ccfe7-458">たとえば、次のコントローラーは `NamespaceRoutingConvention`を使用しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-458">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ccfe7-459">`NamespaceRoutingConvention.Apply` メソッド:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-459">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="ccfe7-460">コントローラーが属性ルーティングされている場合は、何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-460">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="ccfe7-461">`namespace`に基づいてコントローラーテンプレートを設定し、ベース `namespace` 削除します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-461">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="ccfe7-462">`NamespaceRoutingConvention` は `Startup.ConfigureServices`で適用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-462">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="ccfe7-463">たとえば、次のコントローラーを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-463">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="ccfe7-464">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-464">In the preceding code:</span></span>

* <span data-ttu-id="ccfe7-465">基本 `namespace` が `My.Application`。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-465">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="ccfe7-466">前のコントローラーの完全な名前は `My.Application.Admin.Controllers.UsersController`ます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-466">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="ccfe7-467">`NamespaceRoutingConvention` は、コントローラーテンプレートを `Admin/Controllers/Users/[action]/{id?`に設定します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-467">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="ccfe7-468">`NamespaceRoutingConvention` は、コントローラーの属性として適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-468">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ccfe7-469">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-469">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ccfe7-470">ASP.NET Core アプリでは、従来のルーティングと属性ルーティングの使用を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-470">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ccfe7-471">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-471">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ccfe7-472">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-472">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ccfe7-473">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-473">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ccfe7-474">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-474">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ccfe7-475">コントローラー**のルート属性では、コントローラー**属性の**すべて**のアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-475">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="ccfe7-476">属性ルーティングと従来のルーティングでは、同じルーティングエンジンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-476">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="ccfe7-477">URL 生成とアンビエント値</span><span class="sxs-lookup"><span data-stu-id="ccfe7-477">URL Generation and ambient values</span></span>

<span data-ttu-id="ccfe7-478">アプリでは、ルーティング URL 生成機能を使用して、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-478">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ccfe7-479">Url を生成することで、Url をハードコーディングすることがなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-479">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="ccfe7-480">このセクションでは、MVC によって提供される URL 生成機能について説明し、URL の生成のしくみの基本についてのみ説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-480">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="ccfe7-481">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-481">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ccfe7-482"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> インターフェイスは、MVC と URL 生成のルーティングの間のインフラストラクチャの基盤となる要素です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-482">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ccfe7-483">`IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビューコンポーネントの `Url` プロパティを使用して取得できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-483">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ccfe7-484">次の例では、`Controller.Url` プロパティを介して `IUrlHelper` インターフェイスを使用して、別のアクションへの URL を生成しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-484">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ccfe7-485">アプリが既定のルートを使用している場合、`url` 変数の値は `/UrlGeneration/Destination`URL パス文字列になります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-485">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ccfe7-486">この URL パスは、次の組み合わせによってルーティングによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-486">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="ccfe7-487">現在の要求からのルート値 (**アンビエント値**と呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-487">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="ccfe7-488">`Url.Action` に渡され、それらの値をルートテンプレートに置き換える値。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-488">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ccfe7-489">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-489">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ccfe7-490">値を持たないルートパラメーターには、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-490">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="ccfe7-491">既定値がある場合は、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-491">Use a default value if it has one.</span></span>
* <span data-ttu-id="ccfe7-492">省略可能な場合はスキップします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-492">Be skipped if it's optional.</span></span> <span data-ttu-id="ccfe7-493">たとえば、ルートテンプレートの `id` `{controller}/{action}/{id?}`です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-493">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="ccfe7-494">必要なルートパラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-494">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ccfe7-495">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-495">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ccfe7-496">上記の `Url.Action` の例では、[従来のルーティング](#cr)が想定されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-496">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="ccfe7-497">URL の生成は、[属性ルーティング](#ar)と同様に機能しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-497">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="ccfe7-498">従来のルーティングの場合:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-498">With conventional routing:</span></span>

* <span data-ttu-id="ccfe7-499">ルート値は、テンプレートを展開するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-499">The route values are used to expand a template.</span></span>
* <span data-ttu-id="ccfe7-500">`controller` と `action` のルート値は、通常、そのテンプレートに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-500">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="ccfe7-501">これは、ルーティングによって一致した Url が規則に準拠しているために機能します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-501">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="ccfe7-502">次の例では、属性ルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-502">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="ccfe7-503">前のコードの `Source` アクションでは `custom/url/to/destination`が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-503">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="ccfe7-504">`IUrlHelper`の代替手段として ASP.NET Core 3.0 に <xref:Microsoft.AspNetCore.Routing.LinkGenerator> が追加されました。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="ccfe7-505">`LinkGenerator` には、同様に柔軟な機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-505">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="ccfe7-506">`IUrlHelper` のメソッドには、`LinkGenerator` に対応するメソッドのファミリもあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-506">Each other the methods on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ccfe7-507">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-507">Generating URLs by action name</span></span>

<span data-ttu-id="ccfe7-508">[Url. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)、 [Linkgenerator、GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)、およびすべての関連するすべてのオーバーロードは、コントローラー名とアクション名を指定してターゲットエンドポイントを生成するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-508">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="ccfe7-509">`Url.Action`を使用する場合、`controller` と `action` の現在のルート値は、ランタイムによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-509">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="ccfe7-510">`controller` と `action` の値は、[アンビエント値](#ambient)と値の両方に含まれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-510">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="ccfe7-511">メソッド `Url.Action` は常に `action` および `controller` の現在の値を使用し、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-511">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="ccfe7-512">ルーティングでは、アンビエント値の値を使用して、URL の生成時に提供されなかった情報を入力しようとします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-512">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="ccfe7-513">アンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }`の `{a}/{b}/{c}/{d}` のようなルートを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-513">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="ccfe7-514">ルーティングには、URL を生成するための十分な情報が含まれており、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-514">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="ccfe7-515">すべてのルートパラメーターに値があるため、ルーティングに十分な情報が得られます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-515">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="ccfe7-516">`{ d = Donovan }` 値が追加された場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-516">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="ccfe7-517">`{ d = David }` 値は無視されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-517">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="ccfe7-518">生成された URL パスは `Alice/Bob/Carol/Donovan`。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-518">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="ccfe7-519">**警告**: URL パスは階層化されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-519">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="ccfe7-520">前の例では、`{ c = Cheryl }` 値が追加された場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-520">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="ccfe7-521">`{ c = Carol, d = David }` 値は両方とも無視されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-521">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="ccfe7-522">`d` の値はなく、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-522">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="ccfe7-523">URL を生成するには、`c` と `d` の必要な値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-523">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="ccfe7-524">既定のルート `{controller}/{action}/{id?}`では、この問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-524">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="ccfe7-525">`Url.Action` は常に `controller` と `action` 値を明示的に指定するため、この問題はほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-525">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="ccfe7-526">Url のいくつかのオーバーロードでは、ルート値オブジェクトを使用して、`controller` と `action`以外のルートパラメーターの値を指定し[ます。](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)</span><span class="sxs-lookup"><span data-stu-id="ccfe7-526">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ccfe7-527">ルート値オブジェクトは、`id`と共によく使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-527">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="ccfe7-528">たとえば、`Url.Action("Buy", "Products", new { id = 17 })` のようにします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-528">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ccfe7-529">ルート値オブジェクト:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-529">The route values object:</span></span>

* <span data-ttu-id="ccfe7-530">慣例により、通常は匿名型のオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-530">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="ccfe7-531">には、`IDictionary<>` または[POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-531">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="ccfe7-532">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-532">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ccfe7-533">上記のコードでは `/Products/Buy/17?color=red`が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-533">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="ccfe7-534">次のコードでは、絶対 URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-534">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="ccfe7-535">絶対 URL を作成するには、次のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-535">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="ccfe7-536">`protocol`を受け入れるオーバーロード。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-536">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="ccfe7-537">たとえば、上記のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-537">For example, the preceding code.</span></span>
* <span data-ttu-id="ccfe7-538">[Linkgenerator. GetUriByAction。](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)既定で絶対 uri を生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-538">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="ccfe7-539">ルートによる Url の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-539">Generate URLs by route</span></span>

<span data-ttu-id="ccfe7-540">前のコードでは、コントローラーとアクション名を渡すことによって URL を生成することを示していました。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-540">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ccfe7-541">`IUrlHelper` は、メソッドの[Url routeurl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)ファミリも提供します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-541">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="ccfe7-542">これらのメソッドは、 [Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)に似ていますが、`action` の現在の値と `controller` はルート値にコピーされません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-542">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ccfe7-543">`Url.RouteUrl`の最も一般的な使用方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-543">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="ccfe7-544">URL を生成するルート名を指定します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-544">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="ccfe7-545">通常、コントローラーまたはアクション名は指定しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-545">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="ccfe7-546">次の Razor ファイルでは、`Destination_Route`への HTML リンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-546">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="ccfe7-547">HTML および Razor での Url の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-547">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="ccfe7-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> では、`<form>` と `<a>` 要素をそれぞれ生成するために、<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> メソッド[html.actionlink と html](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) . を[提供して](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*)います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ccfe7-549">これらのメソッドは、Url を生成するために[url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)メソッドを使用し、同様の引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-549">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ccfe7-550">`Url.RouteUrl` の `HtmlHelper` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-550">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ccfe7-551">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-551">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ccfe7-552">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-552">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ccfe7-553">詳細については、「[フォームのタグヘルパー](xref:mvc/views/working-with-forms) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-553">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="ccfe7-554">ビューの内部では、`IUrlHelper` プロパティを使って任意のアドホック URL 生成に `Url` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-554">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="ccfe7-555">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-555">URL generation in Action Results</span></span>

<span data-ttu-id="ccfe7-556">前の例では、コントローラーで `IUrlHelper` を使用する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-556">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="ccfe7-557">コントローラーで最も一般的に使用されるのは、アクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-557">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ccfe7-558">基底クラス <xref:Microsoft.AspNetCore.Mvc.ControllerBase> および <xref:Microsoft.AspNetCore.Mvc.Controller> では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ccfe7-559">一般的な使用方法の1つは、ユーザー入力を受け入れた後にリダイレクトすることです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-559">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="ccfe7-560"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> や <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> などのアクションの結果ファクトリメソッドは、`IUrlHelper`のメソッドと同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-560">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ccfe7-561">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="ccfe7-561">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ccfe7-562">[従来のルーティング](#cr)では、[専用の従来のルート](#dcr)と呼ばれる特別な種類のルート定義を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-562">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="ccfe7-563">次の例では、`blog` という名前のルートが専用の従来のルートになっています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-563">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ccfe7-564">上記のルート定義を使用すると、`Url.Action("Index", "Home")` は `default` ルートを使用して `/` URL パスを生成しますが、その理由は何でしょうか。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-564">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="ccfe7-565">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-565">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ccfe7-566">[専用の従来のルート](#dcr)では、ルートパラメーターが指定されていない既定値の特殊な動作に依存しています。これにより、ルートが URL 生成に[最長一致](xref:fundamentals/routing#greedy)するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-566">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="ccfe7-567">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-567">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ccfe7-568">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-568">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ccfe7-569">`{ controller = Home, action = Index }` の値が `{ controller = Blog, action = Article }`と一致しないため、`blog` を使用した URL 生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-569">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ccfe7-570">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-570">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ccfe7-571">領域</span><span class="sxs-lookup"><span data-stu-id="ccfe7-571">Areas</span></span>

<span data-ttu-id="ccfe7-572">[区分](xref:mvc/controllers/areas)は、関連する機能を別のグループとしてグループにまとめるために使用される MVC 機能です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-572">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="ccfe7-573">コントローラーアクションのルーティング名前空間。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-573">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="ccfe7-574">ビューのフォルダー構造。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-574">Folder structure for views.</span></span>

<span data-ttu-id="ccfe7-575">区分を使用すると、異なる領域がある限り、同じ名前の複数のコントローラーをアプリに割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-575">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="ccfe7-576">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-576">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ccfe7-577">ここでは、ルーティングと領域の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-577">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="ccfe7-578">領域をビューで使用する方法の詳細については、「[区分](xref:mvc/controllers/areas)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-578">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="ccfe7-579">次の例では、既定の従来のルートと、`Blog`という名前の `area` の `area` ルートを使用するように MVC を構成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-579">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ccfe7-580">前のコードでは、`"blog_route"`を作成するために <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-580">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="ccfe7-581">2番目のパラメーター `"Blog"`は、領域名です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-581">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="ccfe7-582">`/Manage/Users/AddUser`のような URL パスと一致すると、`"blog_route"` ルートによって `{ area = Blog, controller = Users, action = AddUser }`ルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-582">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ccfe7-583">`area` ルート値は、`area`の既定値によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-583">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="ccfe7-584">`MapAreaControllerRoute` によって作成されたルートは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-584">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="ccfe7-585">`MapAreaControllerRoute` は、指定された区分名 (この場合は`area`) を使い、既定値と、`Blog` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-585">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ccfe7-586">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-586">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="ccfe7-587">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-587">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ccfe7-588">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-588">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ccfe7-589">前の例を使用すると、ルート値 `{ area = Blog, controller = Users, action = AddUser }` 次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-589">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ccfe7-590">[[区分]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性は、1つの領域の一部としてコントローラーを表します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-590">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="ccfe7-591">このコントローラーは `Blog` 領域にあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-591">This controller is in the `Blog` area.</span></span> <span data-ttu-id="ccfe7-592">`[Area]` 属性のないコントローラーは、どの領域のメンバーでもなく、`area` ルート値がルーティングによって指定されている場合は一致しませ**ん**。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-592">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ccfe7-593">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-593">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="ccfe7-594">各コントローラーの名前空間は、完全を期すためにここに示されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-594">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="ccfe7-595">前のコントローラーで同じ名前空間が使用されている場合は、コンパイラエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-595">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="ccfe7-596">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-596">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ccfe7-597">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-597">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ccfe7-598">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-598">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="ccfe7-599">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-599">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ccfe7-600">領域内でアクションを実行する場合、`area` のルート値は、URL の生成に使用するルーティングの[アンビエント値](#ambient)として使用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-600">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="ccfe7-601">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-601">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="ccfe7-602">次のコードでは `/Zebra/Users/AddUser`の URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-602">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="ccfe7-603">アクションの定義</span><span class="sxs-lookup"><span data-stu-id="ccfe7-603">Action definition</span></span>

<span data-ttu-id="ccfe7-604">[非 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性を持つパブリックメソッドは、アクションです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-604">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ccfe7-605">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="ccfe7-605">Sample code</span></span>

 * <span data-ttu-id="ccfe7-606">[Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-606">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="ccfe7-607">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-607">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ccfe7-608">ASP.NET Core MVC は、ルーティング [ミドルウェア](xref:fundamentals/middleware/index)を使って、受信した要求の URL を照合し、アクションにマップします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-608">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="ccfe7-609">ルートは、スタートアップ コードまたは属性で定義されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-609">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="ccfe7-610">ルートでは、URL パスとアクションの照合方法が記述されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-610">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="ccfe7-611">また、ルートは、応答で送信される (リンク用) の URL の生成にも使われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-611">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="ccfe7-612">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-612">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ccfe7-613">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-613">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ccfe7-614">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-614">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ccfe7-615">このドキュメントでは、MVC とルーティングの間の相互作用、および標準的な MVC アプリでのルーティング機能の利用方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-615">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="ccfe7-616">高度なルーティングについて詳しくは、「[ASP.NET Core のルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-616">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="ccfe7-617">ルーティング ミドルウェアの設定</span><span class="sxs-lookup"><span data-stu-id="ccfe7-617">Setting up Routing Middleware</span></span>

<span data-ttu-id="ccfe7-618">*Configure* メソッドには、次のようなコードが含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-618">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ccfe7-619">`UseMvc` の呼び出しの内部で、`MapRoute` は単一のルートの作成に使われます。これは、`default` ルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-619">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="ccfe7-620">ほとんどの MVC アプリは、`default` ルートのようなルートをテンプレートで使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-620">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="ccfe7-621">ルート テンプレート `"{controller=Home}/{action=Index}/{id?}"` は、`/Products/Details/5` のような URL パスと一致し、パスをトークン化することによってルート値 `{ controller = Products, action = Details, id = 5 }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-621">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ccfe7-622">MVC は、`ProductsController` という名前のコントローラーを探して、アクション `Details` の実行を試みます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-622">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="ccfe7-623">この例では、このアクションを呼び出すときに、モデル バインドが `id = 5` という値を使って、`id` パラメーターを `5` に設定することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-623">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="ccfe7-624">詳しくは、「[モデル バインド](../models/model-binding.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-624">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="ccfe7-625">`default` ルートの使用:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-625">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ccfe7-626">ルート テンプレート:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-626">The route template:</span></span>

* <span data-ttu-id="ccfe7-627">`{controller=Home}` は、`Home` を既定の `controller` として定義します</span><span class="sxs-lookup"><span data-stu-id="ccfe7-627">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="ccfe7-628">`{action=Index}` は、`Index` を既定の `action` として定義します</span><span class="sxs-lookup"><span data-stu-id="ccfe7-628">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="ccfe7-629">`{id?}` は、`id` を省略可能として定義します</span><span class="sxs-lookup"><span data-stu-id="ccfe7-629">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="ccfe7-630">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-630">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ccfe7-631">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-631">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="ccfe7-632">`"{controller=Home}/{action=Index}/{id?}"` は URL パス `/` と一致でき、ルート値 `{ controller = Home, action = Index }` を生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-632">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="ccfe7-633">`controller` と `action` の値は既定値を使い、`id` は URL パスに対応するセグメントが存在しないため値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-633">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ccfe7-634">MVC はこれらのルート値を使って、`HomeController` および `Index` アクションを選びます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-634">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ccfe7-635">このコントローラー定義とルート テンプレートを使うと、`HomeController.Index` アクションは次のいずれかの URL パスに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-635">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="ccfe7-636">便利なメソッド `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="ccfe7-636">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="ccfe7-637">これは、次の代わりに使うことができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-637">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ccfe7-638">`UseMvc` および `UseMvcWithDefaultRoute` は、`RouterMiddleware` のインスタンスをミドルウェア パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-638">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="ccfe7-639">MVC は、ミドルウェアと直接相互作用せず、ルーティングを使って要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-639">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="ccfe7-640">MVC は、`MvcRouteHandler` のインスタンスによってルートに接続されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-640">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="ccfe7-641">`UseMvc` の内部のコードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-641">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="ccfe7-642">`UseMvc` は、ルートを直接定義することはなく、`attribute` ルートのルート コレクションにプレースホルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-642">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="ccfe7-643">オーバーロード `UseMvc(Action<IRouteBuilder>)` は、独自ルートの追加を可能にし、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-643">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="ccfe7-644">`UseMvc` とそのすべてのバリエーションは、属性ルートのプレースホルダーを追加します。属性ルーティングは、`UseMvc` の構成方法に関係なく常に利用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-644">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="ccfe7-645">`UseMvcWithDefaultRoute` は既定のルートを定義し、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-645">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="ccfe7-646">「[属性ルーティング](#attribute-routing-ref-label)」セクションでは、属性ルーティングについてさらに詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-646">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ccfe7-647">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-647">Conventional routing</span></span>

<span data-ttu-id="ccfe7-648">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-648">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ccfe7-649">前のコードは、従来のルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-649">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="ccfe7-650">このスタイルは、URL パスの*規則*を確立するため、従来のルーティングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-650">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ccfe7-651">最初のパスセグメントは、コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-651">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="ccfe7-652">2つ目は、アクション名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-652">The second maps to the action name.</span></span>
* <span data-ttu-id="ccfe7-653">3番目のセグメントは、オプションの `id`に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-653">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="ccfe7-654">`id` は、モデルエンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-654">`id` maps to a model entity.</span></span>

<span data-ttu-id="ccfe7-655">この `default` ルートを使うと、URL パス `/Products/List` は `ProductsController.List` アクションにマップし、`/Blog/Article/17` は `BlogController.Article` にマップします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-655">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="ccfe7-656">このマッピングは、コントローラーとアクションの名前に**のみ**基づき、名前空間、ソース ファイルの場所、またはメソッドのパラメーターには基づきません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-656">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="ccfe7-657">既定のルートで規則ルーティングを使うと、定義するアクションごとに新しい URL パターンを考える必要がなく、アプリケーションをすばやく構築できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-657">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="ccfe7-658">CRUD スタイルのアクションを使うアプリケーションでは、コントローラー間で URL に一貫性を持たせると、コードが容易になり、UI の予測可能性が向上します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-658">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ccfe7-659">`id` はルート テンプレートで省略可能と定義されており、これは URL の一部として ID を提供されなくてもアクションを実行できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-659">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="ccfe7-660">通常、`id` が URL から省略されると、ID はモデル バインドによって `0` に設定され、結果として、`id == 0` と一致するエンティティはデータベースで検索されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-660">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="ccfe7-661">属性ルーティングを使うと、ID が必須のアクションと必須ではないアクションをきめ細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-661">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ccfe7-662">慣例に従って、ドキュメントでは `id` などの省略可能なパラメーターが正しい使用法で使われる可能性がある場合はパラメーターを記載してあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-662">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="ccfe7-663">複数のルート</span><span class="sxs-lookup"><span data-stu-id="ccfe7-663">Multiple routes</span></span>

<span data-ttu-id="ccfe7-664">`UseMvc` の呼び出しをさらに追加することで、`MapRoute` の内部に複数のルートを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-664">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="ccfe7-665">これにより、次のように、複数の規則を定義すること、または特定のアクションに専用の規則ルートを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-665">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ccfe7-666">`blog` ルートは "*専用の規則ルート*" です。これは、このルートは規則ルーティング システムを使いますが、特定のアクション専用であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-666">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="ccfe7-667">`controller` と `action` はパラメーターとしてルート テンプレートに表示されないので、既定値を持つことだけができ、したがってこのルートは常に `BlogController.Article` アクションにマップされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-667">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ccfe7-668">ルート コレクション内のルートには順序があり、追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-668">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="ccfe7-669">そのため、この例では、`blog` ルートが `default` ルートより先に試されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-669">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="ccfe7-670">*専用の従来のルート*では、多くの場合、`{*article}` のような**キャッチオール**ルートパラメーターを使用して、URL パスの残りの部分をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-670">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="ccfe7-671">このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-671">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ccfe7-672">この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-672">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="ccfe7-673">フォールバック</span><span class="sxs-lookup"><span data-stu-id="ccfe7-673">Fallback</span></span>

<span data-ttu-id="ccfe7-674">要求処理の一環として、MVC はルートの値を使ってアプリケーション内のコントローラーとアクションを検索できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-674">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="ccfe7-675">ルートの値がアクションと一致しない場合、そのルートは一致と見なされず、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-675">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="ccfe7-676">これは "*フォールバック*" と呼ばれ、規則ルートがオーバーラップしている場合の簡略化を意図したものです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-676">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="ccfe7-677">アクションの明確化</span><span class="sxs-lookup"><span data-stu-id="ccfe7-677">Disambiguating actions</span></span>

<span data-ttu-id="ccfe7-678">2 つのアクションがルーティングで一致する場合、MVC はあいまいさを解消して "最善の" 候補を選ぶか、または例外をスローする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-678">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="ccfe7-679">例 :</span><span class="sxs-lookup"><span data-stu-id="ccfe7-679">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="ccfe7-680">このコントローラーでは、URL パス `/Products/Edit/17` およびルート データ `{ controller = Products, action = Edit, id = 17 }` と一致する 2 つのアクションが定義されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-680">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="ccfe7-681">これは、`Edit(int)` で製品を編集するためのフォームを表示し、`Edit(int, Product)` で送信されたフォームを処理する MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-681">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="ccfe7-682">これを MVC で可能にするには、要求が HTTP `Edit(int, Product)` の場合は `POST` を選び、それ以外の HTTP 動詞の場合は `Edit(int)` を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-682">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="ccfe7-683">`HttpPostAttribute` (`[HttpPost]`) は、HTTP 動詞が `IActionConstraint` の場合にのみそのアクションの選択を許可する `POST` の実装です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-683">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ccfe7-684">`IActionConstraint` が存在すると、`Edit(int, Product)` の方が `Edit(int)` より "良い" 一致になるので、`Edit(int, Product)` が最初に試されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-684">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="ccfe7-685">カスタム `IActionConstraint` を作成する必要があるのは特殊なシナリオだけですが、`HttpPostAttribute` のような属性の役割を理解しておくことが重要です。似た属性が他の HTTP 動詞に対しても定義されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-685">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="ccfe7-686">規則ルーティングでは、アクションが `show form -> submit form` ワークフローの一部になっている場合、複数のアクションが同じアクション名を使うのはよくあることです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-686">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="ccfe7-687">「[IActionConstraint について](#understanding-iactionconstraint)」セクションを読むと、このパターンの便利さがいっそうよくわかります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-687">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="ccfe7-688">複数のルートが一致し、MVC が "最善の " ルートを発見できない場合、MVC は `AmbiguousActionException` をスローします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-688">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="ccfe7-689">ルート名</span><span class="sxs-lookup"><span data-stu-id="ccfe7-689">Route names</span></span>

<span data-ttu-id="ccfe7-690">次の例の文字列 `"blog"` と `"default"` は、ルート名です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-690">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ccfe7-691">ルート名は、URL の生成に名前付きのルートを使うことができるよう、ルートに論理名を提供します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-691">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="ccfe7-692">これは、ルートの順序指定によって URL の生成が複雑になる場合に、URL の作成を大幅に合理化します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-692">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ccfe7-693">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-693">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ccfe7-694">ルート名が URL の照合や要求の処理に影響を与えることはありません。ルート名は URL の生成のみに使われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-694">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="ccfe7-695">MVC 固有のヘルパーでの URL の生成など、URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-695">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="ccfe7-696">属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-696">Attribute routing</span></span>

<span data-ttu-id="ccfe7-697">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-697">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ccfe7-698">次の例では、`app.UseMvc();` が `Configure` メソッド内で使われており、ルートは渡されていません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-698">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="ccfe7-699">`HomeController` は、既定のルート `{controller=Home}/{action=Index}/{id?}` が一致するのと同じように、URL のセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-699">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="ccfe7-700">`HomeController.Index()` アクションは、URL パス `/`、`/Home`、または `/Home/Index` のいずれかに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-700">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="ccfe7-701">この例では、属性ルーティングと規則ルーティングでのプログラミングの大きな違いが強調して示されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-701">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="ccfe7-702">属性ルーティングの方が、ルートを指定するために多くの入力を必要とします。既定の規則ルートの方が簡潔にルートを処理します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-702">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ccfe7-703">ただし、属性ルーティングでは、各アクションに適用するルート テンプレートを正確に制御できます (そして制御する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-703">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="ccfe7-704">属性ルーティングでは、コントローラー名とアクション名はアクションの選択において役割を**持ちません**。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-704">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="ccfe7-705">この例では、前の例と同じ URL を照合します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-705">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="ccfe7-706">上のルート テンプレートでは、`action`、`area`、`controller` のルート パラメーターは定義されていません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-706">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="ccfe7-707">実際、これらのルート パラメーターは属性ルートでは許可されていません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-707">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="ccfe7-708">ルート テンプレートはアクションと既に関連付けられているため、URL からアクション名を解析しても意味はありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-708">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="ccfe7-709">Http[Verb] 属性を使用する属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-709">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="ccfe7-710">属性ルーティングでは、`Http[Verb]` などの `HttpPostAttribute` 属性も使うことができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-710">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="ccfe7-711">これらの属性はすべて、ルート テンプレートを受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-711">All of these attributes can accept a route template.</span></span> <span data-ttu-id="ccfe7-712">次の例では、同じルート テンプレートと一致する 2 つのアクションが示されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-712">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="ccfe7-713">`/products` のような URL パスの場合、HTTP 動詞が `ProductsApi.ListProducts` のときは `GET` アクションが実行され、HTTP 動詞が `ProductsApi.CreateProduct` のときは `POST` が実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-713">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ccfe7-714">属性ルーティングでは、最初に、ルート属性で定義されているルート テンプレートのセットに対して URL が照合されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-714">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="ccfe7-715">ルート テンプレートが一致すると、`IActionConstraint` 制約が適用されて、実行できるアクションが決定されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-715">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="ccfe7-716">REST API を構築する場合、アクションではすべての HTTP メソッドが受け入れられるので、アクション メソッドに対して `[Route(...)]` を使用することはまれです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-716">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="ccfe7-717">より固有の `Http*Verb*Attributes` を使って、API がサポートするものを正確に指定するのがよい方法です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-717">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="ccfe7-718">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-718">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ccfe7-719">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-719">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ccfe7-720">次の例では、`id` は URL パスの一部として必須です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-720">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ccfe7-721">`ProductsApi.GetProduct(int)` アクションは、`/products/3` のような URL パスに対しては実行されますが、`/products` のような URL パスに対しては実行されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-721">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="ccfe7-722">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-722">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="ccfe7-723">ルート名</span><span class="sxs-lookup"><span data-stu-id="ccfe7-723">Route Name</span></span>

<span data-ttu-id="ccfe7-724">次のコードでは、 *の "* ルート名`Products_List`" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-724">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ccfe7-725">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-725">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ccfe7-726">ルート名は、ルーティングの URL 照合動作には影響を与えず、URL 生成に対してのみ使われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-726">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="ccfe7-727">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-727">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="ccfe7-728">これに対し、規則 "*既定ルート*" では、`id` パラメーターは省略可能 (`{id?}`) として定義されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-728">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ccfe7-729">API を厳密に指定するこの機能には、`/products` と `/products/5` を異なるアクションに対してディスパッチできるといった利点があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-729">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="ccfe7-730">ルートの結合</span><span class="sxs-lookup"><span data-stu-id="ccfe7-730">Combining routes</span></span>

<span data-ttu-id="ccfe7-731">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-731">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ccfe7-732">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-732">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ccfe7-733">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-733">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ccfe7-734">次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-734">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="ccfe7-735">どちらのアクションも、`GET` でマークされているため、HTTP `HttpGetAttribute` だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-735">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="ccfe7-736">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-736">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ccfe7-737">次の例は、"*既定ルート*" と同様の URL パスのセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-737">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="ccfe7-738">属性ルートの順序の指定</span><span class="sxs-lookup"><span data-stu-id="ccfe7-738">Ordering attribute routes</span></span>

<span data-ttu-id="ccfe7-739">定義された順序で実行される従来のルートとは対照的に、属性ルーティングはツリーを構築し、すべてのルートを同時に照合します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-739">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="ccfe7-740">これは、ルート エントリが理想的な順序で配置されているかのように動作します。一般的なルートより前に、最も固有のルートが実行する機会があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-740">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ccfe7-741">たとえば、`blog/search/{topic}` のようなルートは、`blog/{*article}` のようなルートより固有です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-741">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="ccfe7-742">論理的には、`blog/search/{topic}` ルートが唯一の意味のある順序なので、既定では、これが最初に "実行" されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-742">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="ccfe7-743">規則ルーティングを使うときは、開発者が目的の順序でルートを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-743">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ccfe7-744">属性ルートでは、フレームワークが提供するすべてのルート属性の `Order` プロパティを使って、順序を構成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-744">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="ccfe7-745">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-745">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ccfe7-746">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-746">The default order is `0`.</span></span> <span data-ttu-id="ccfe7-747">`Order = -1` を使ってルートを設定すると、順序が設定されていないルートの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-747">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="ccfe7-748">`Order = 1` を使ってルートを設定すると、既定のルート順序の後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-748">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="ccfe7-749">`Order` には依存しないでください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-749">Avoid depending on `Order`.</span></span> <span data-ttu-id="ccfe7-750">URL 空間で正しくルーティングするために明示的な順序値が必要な場合、クライアントの混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-750">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ccfe7-751">一般に、属性ルーティングは URL 照合で正しいルートを選びます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-751">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="ccfe7-752">URL の生成に使われる既定の順序がうまくいかない場合は、通常、オーバーライドとしてルート名を使う方が、`Order` プロパティを適用するより簡単です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-752">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="ccfe7-753">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-753">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ccfe7-754">Razor Pages でのルート順序に関する情報は、[Razor Pages のルートとアプリの規則:ルート順序](xref:razor-pages/razor-pages-conventions#route-order)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-754">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ccfe7-755">ルート テンプレートでのトークンの置換 ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="ccfe7-755">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="ccfe7-756">利便性のため、属性ルートではトークンを角かっこ ( *、* ) で囲むことによる "`[`トークンの置換`]`" がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-756">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="ccfe7-757">トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-757">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="ccfe7-758">次の例では、アクションはコメントで説明されているように URL パスと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-758">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ccfe7-759">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-759">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ccfe7-760">上の例は、次のコードと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-760">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ccfe7-761">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-761">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ccfe7-762">これをトークンの置換と組み合わせると特に強力です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-762">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="ccfe7-763">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-763">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="ccfe7-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="ccfe7-765">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-765">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ccfe7-766">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="ccfe7-766">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ccfe7-767">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-767">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ccfe7-768">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-768">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ccfe7-769">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-769">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ccfe7-770">`RouteTokenTransformerConvention` は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-770">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="ccfe7-771">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-771">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ccfe7-772">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-772">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="ccfe7-773">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-773">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="ccfe7-774">複数のルート</span><span class="sxs-lookup"><span data-stu-id="ccfe7-774">Multiple Routes</span></span>

<span data-ttu-id="ccfe7-775">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-775">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ccfe7-776">これの最も一般的な使用方法は、次の例で示すように、"*既定の規則ルート*" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-776">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="ccfe7-777">コントローラーに複数のルート属性を配置すると、それぞれが、アクション メソッドの各ルート属性と結合します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-777">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="ccfe7-778">アクションに (`IActionConstraint` を実装する) 複数のルート属性を配置すると、各アクション制約がそれを定義した属性のルート テンプレートと結合します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-778">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="ccfe7-779">アクションで複数のルートを使うと強力に見えますが、アプリケーションの URL 空間は簡潔で明確に定義された状態に保つことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-779">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="ccfe7-780">アクションで複数のルートを使うのは、必要な場合 (既存のクライアントをサポートする、など) だけにしてください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-780">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ccfe7-781">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="ccfe7-781">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ccfe7-782">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-782">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="ccfe7-783">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-783">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ccfe7-784">`IRouteTemplateProvider` を使用するカスタム ルート属性</span><span class="sxs-lookup"><span data-stu-id="ccfe7-784">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="ccfe7-785">フレームワークで提供されるすべてのルート属性 (`[Route(...)]`、`[HttpGet(...)]` など) は、`IRouteTemplateProvider` インターフェイスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-785">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="ccfe7-786">アプリが起動し、`IRouteTemplateProvider` を実装している属性を使ってルートの初期セットを作成すると、MVC はコントローラー クラスとアクション メソッドで属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-786">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ccfe7-787">`IRouteTemplateProvider` を実装して、独自のルート属性を定義できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-787">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="ccfe7-788">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-788">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="ccfe7-789">上の例の属性は、`Template` が適用されると、自動的に `"api/[controller]"` を `[MyApiController]` に設定します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-789">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ccfe7-790">アプリケーション モデルを使用した属性ルートのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="ccfe7-790">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="ccfe7-791">"*アプリケーション モデル*" は、アクションをルーティングおよび実行するために MVC によって使われるすべてのメタデータで起動時に作成されるオブジェクト モデルです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-791">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="ccfe7-792">"*アプリケーション モデル*" には、(`IRouteTemplateProvider` によって) ルート属性から収集されるすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-792">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="ccfe7-793">起動時にアプリケーション モデルを変更してルーティングの動作方法をカスタマイズする "*規則*" を作成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-793">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="ccfe7-794">このセクションでは、アプリケーション モデルを使ってルーティングをカスタマイズする簡単な例を示します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-794">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ccfe7-795">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="ccfe7-795">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ccfe7-796">MVC アプリケーションでは、規則ルーティングと属性ルーティングを併用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-796">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ccfe7-797">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-797">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ccfe7-798">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-798">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ccfe7-799">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-799">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ccfe7-800">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-800">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ccfe7-801">コントローラーの**任意**のルート属性により、コントローラー属性のすべてのアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-801">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="ccfe7-802">2 種類のルーティング システムを区別しているのは、URL がルート テンプレートと一致した後に適用されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-802">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="ccfe7-803">規則ルーティングでは、一致のルート値を使って、規則ルーティングされるすべてのアクションの参照テーブルから、アクションとコントローラーが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-803">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="ccfe7-804">属性ルーティングでは、各テンプレートはアクションと既に関連付けられており、それ以上参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-804">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="ccfe7-805">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="ccfe7-805">Complex segments</span></span>

<span data-ttu-id="ccfe7-806">複雑なセグメント (たとえば、`[Route("/dog{token}cat")]`) は、右から左にリテラルを最短の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-806">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="ccfe7-807">説明については、[ソース コード](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-807">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="ccfe7-808">詳しくは、[こちらの懸案事項](https://github.com/dotnet/AspNetCore.Docs/issues/8197)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-808">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="ccfe7-809">URL の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-809">URL Generation</span></span>

<span data-ttu-id="ccfe7-810">MVC アプリケーションでは、ルーティングの URL 生成機能を使って、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-810">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ccfe7-811">URL を生成すると URL をハードコーディングする必要がなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-811">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="ccfe7-812">このセクションでは、MVC によって提供される URL 生成機能について説明します。URL 生成のしくみに関する基本だけを取り上げます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-812">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="ccfe7-813">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-813">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ccfe7-814">`IUrlHelper` インターフェイスは、MVC と URL 生成のルーティングの間にあるインフラストラクチャの基盤部分です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-814">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ccfe7-815">使用可能な `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビュー コンポーネントの `Url` プロパティを使って探します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-815">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ccfe7-816">この例の `IUrlHelper` インターフェイスは、別のアクションへの URL を生成するために `Controller.Url` プロパティを介して使われています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-816">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ccfe7-817">アプリケーションが既定の規則ルートを使っている場合、`url` 変数の値は URL パス文字列 `/UrlGeneration/Destination` になります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-817">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ccfe7-818">この URL パスは、ルーティングにより、現在の要求からのルート値 (アンビエント値) と、`Url.Action` に渡された値を結合し、これらの値でルート テンプレートを置換することによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-818">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ccfe7-819">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-819">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ccfe7-820">値を持たないルート パラメーターは、既定値がある場合はそれを使うことができ、省略可能な場合はスキップできます (この例の `id` の場合と同様)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-820">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="ccfe7-821">必須のルート パラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-821">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ccfe7-822">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-822">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ccfe7-823">上の `Url.Action` の例では規則ルーティングを想定しています。URL 生成は属性ルーティングでも同じように動作しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-823">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="ccfe7-824">規則ルーティングでは、ルート値を使ってテンプレートが展開され、通常、`controller` と `action` のルートがそのテンプレートに表示されます。これは、ルーティングによって一致した URL が "*規則*" に従うために動作します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-824">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="ccfe7-825">属性ルーティングでは、`controller` と `action` のルート値はテンプレートに表示できません。代わりに、使うテンプレートの検索に使われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-825">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="ccfe7-826">この例では、属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-826">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="ccfe7-827">MVC はすべての属性ルーティングされるアクションの参照テーブルを作成し、`controller` と `action` の値で照合して、URL 生成に使うルート テンプレートを選びます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-827">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="ccfe7-828">上の例では、`custom/url/to/destination` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-828">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ccfe7-829">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-829">Generating URLs by action name</span></span>

<span data-ttu-id="ccfe7-830">`Url.Action` (`IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="ccfe7-830">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="ccfe7-831">`Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-831">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="ccfe7-832">`Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"*アンビエント値*" **と** "*値*" 両方の一部です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-832">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="ccfe7-833">`Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-833">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="ccfe7-834">ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-834">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="ccfe7-835">`{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-835">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="ccfe7-836">値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-836">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="ccfe7-837">URL パスは階層的です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-837">URL paths are hierarchical.</span></span> <span data-ttu-id="ccfe7-838">上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-838">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="ccfe7-839">この場合、`d` には値がなくなり、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-839">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="ccfe7-840">`c` および `d` の目的の値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-840">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="ccfe7-841">既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-841">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="ccfe7-842">`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-842">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ccfe7-843">これを最もよく目にするのは、`id` のように `Url.Action("Buy", "Products", new { id = 17 })` で使われる場合です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-843">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ccfe7-844">慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-844">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="ccfe7-845">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-845">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="ccfe7-846">絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="ccfe7-846">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="ccfe7-847">ルートによる URL の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-847">Generating URLs by route</span></span>

<span data-ttu-id="ccfe7-848">上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-848">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ccfe7-849">`IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-849">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="ccfe7-850">これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-850">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ccfe7-851">最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-851">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="ccfe7-852">HTML での URL の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-852">Generating URLs in HTML</span></span>

<span data-ttu-id="ccfe7-853">`IHtmlHelper` で提供されている `HtmlHelper` メソッドの `Html.BeginForm` および `Html.ActionLink` は、それぞれ `<form>` 要素と `<a>` 要素を生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-853">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ccfe7-854">これらのメソッドは `Url.Action` メソッドを使って URL を生成するので、同じような引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-854">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ccfe7-855">`Url.RouteUrl` の `HtmlHelper` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-855">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ccfe7-856">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-856">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ccfe7-857">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-857">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ccfe7-858">詳しくは、「[フォームの操作](../views/working-with-forms.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-858">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="ccfe7-859">ビューの内部では、`IUrlHelper` プロパティを使って任意のアドホック URL 生成に `Url` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-859">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="ccfe7-860">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="ccfe7-860">Generating URLS in Action Results</span></span>

<span data-ttu-id="ccfe7-861">上の例ではコントローラーでの `IUrlHelper` の使用が示されていますが、コントローラーでの最も一般的な使用方法はアクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-861">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ccfe7-862">基底クラス `ControllerBase` および `Controller` では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-862">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ccfe7-863">一般的な使用法の 1 つは、ユーザー入力を受け付けた後でリダイレクトする場合です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-863">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="ccfe7-864">アクション結果ファクトリ メソッドは、`IUrlHelper` のメソッドに似たパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-864">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ccfe7-865">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="ccfe7-865">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ccfe7-866">規則ルーティングでは、"*専用規則ルート*" と呼ばれる特殊なルート定義を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-866">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="ccfe7-867">次の例の `blog` という名前のルートが専用規則ルートです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-867">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ccfe7-868">これらのルート定義を使うと、`Url.Action("Index", "Home")` は `/` ルートで URL パス `default` を生成します。なぜでしょうか。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-868">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="ccfe7-869">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-869">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ccfe7-870">専用規則ルートは、URL の生成でルートの "一致範囲が広くなりすぎる" のを防ぐために対応するルート パラメーターを持たない既定値の特別な動作に依存します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-870">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="ccfe7-871">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-871">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ccfe7-872">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-872">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ccfe7-873">値 `blog` は `{ controller = Home, action = Index }` と一致しないため、`{ controller = Blog, action = Article }` を使った URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-873">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ccfe7-874">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-874">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ccfe7-875">領域</span><span class="sxs-lookup"><span data-stu-id="ccfe7-875">Areas</span></span>

<span data-ttu-id="ccfe7-876">[区分](areas.md)は MVC の機能であり、関連する機能を独立したルーティング名前空間 (コントローラー アクションの場合) およびフォルダー構造 (ビューの場合) としてグループ化するために使われます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-876">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="ccfe7-877">区分を使うと、アプリケーションは同じ名前の複数のコントローラーを持つことができます (ただし、"*区分*" が異なる場合)。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-877">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="ccfe7-878">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-878">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ccfe7-879">このセクションでは、ルーティングと区分がどのように相互作用するのかについて説明します。ビューでの区分の使い方について詳しくは、「[区分](areas.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-879">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="ccfe7-880">次の例では、既定の規則ルートと、 *という名前の区分に対する "* 区分ルート`Blog`" を使うように、MVC を構成しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-880">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ccfe7-881">`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-881">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ccfe7-882">`area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-882">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="ccfe7-883">`MapAreaRoute` は、指定された区分名 (この場合は`area`) を使い、既定値と、`Blog` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-883">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ccfe7-884">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-884">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="ccfe7-885">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-885">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ccfe7-886">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-886">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ccfe7-887">上の例を使うと、ルート値は次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-887">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ccfe7-888">`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-888">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="ccfe7-889">`[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、 **ルート値がルーティングによって提供されたときは一致**しません`area`。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-889">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ccfe7-890">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-890">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="ccfe7-891">ここでは完全を期すために各コントローラーの名前空間を示してあります。そうしないと、コントローラーの名前が競合し、コンパイラ エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-891">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="ccfe7-892">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-892">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ccfe7-893">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-893">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ccfe7-894">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-894">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="ccfe7-895">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-895">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ccfe7-896">区分の内部でアクションを実行するとき、`area` のルート値は、URL の生成に使うルーティングの "*アンビエント値*" として利用できます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-896">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="ccfe7-897">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-897">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="ccfe7-898">IActionConstraint について</span><span class="sxs-lookup"><span data-stu-id="ccfe7-898">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="ccfe7-899">このセクションでは、フレームワークの内部構造と MVC が実行するアクションを選ぶ方法について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-899">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="ccfe7-900">一般的なアプリケーションでは、カスタム `IActionConstraint` は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-900">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="ccfe7-901">インターフェイスにあまり詳しくない場合でも、既に `IActionConstraint` を使ったことがあるはずです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-901">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="ccfe7-902">`[HttpGet]` 属性およびそれに似た `[Http-VERB]` 属性は、アクション メソッドの実行を制限するために `IActionConstraint` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-902">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="ccfe7-903">既定の規則ルートでは、URL パス `/Products/Edit` は値 `{ controller = Products, action = Edit }` を生成し、ここで示す**両方**のアクションと一致するものとします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-903">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="ccfe7-904">`IActionConstraint` ではこれを、両方のアクションが候補と見なされる、といいます。どちらもルート データと一致するからです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-904">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="ccfe7-905">`HttpGetAttribute` は実行すると、*Edit()* は *GET* に対して一致し、他の HTTP 動詞には一致しないことを示します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-905">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="ccfe7-906">`Edit(...)` アクションには制約が定義されていないので、すべての HTTP 動詞と一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-906">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="ccfe7-907">したがって、`POST` は `Edit(...)` のみと一致します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-907">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="ccfe7-908">一方、`GET` の場合は両方のアクションが一致します。しかし、`IActionConstraint` を指定されているアクションの方が常に、指定されていないアクション "*より適切*" であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-908">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="ccfe7-909">したがって、`Edit()` には `[HttpGet]` があるので、より具体的と見なされ、両方のアクションが一致する場合はこちらが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-909">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="ccfe7-910">概念的には、`IActionConstraint` は "*オーバーロード*" の形式ですが、同じ名前のメソッドをオーバーロードするのではなく、同じ URL に一致するアクション間でオーバーロードします。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-910">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="ccfe7-911">属性ルーティングも `IActionConstraint` を使い、異なるコントローラーのアクションがどちらも候補と見なされる結果になることがあります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-911">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="ccfe7-912">IActionConstraint の実装</span><span class="sxs-lookup"><span data-stu-id="ccfe7-912">Implementing IActionConstraint</span></span>

<span data-ttu-id="ccfe7-913">`IActionConstraint` を実装する最も簡単な方法は、`System.Attribute` の派生クラスを作成し、アクションとコントローラーに配置する方法です。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-913">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="ccfe7-914">MVC は、属性として適用された `IActionConstraint` を自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-914">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="ccfe7-915">アプリケーション モデルを使って制約を適用することができ、適用方法をメタプログラムできるので、おそらくこれが最も柔軟なアプローチです。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-915">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="ccfe7-916">次の例では、制約は、ルートデータから*国コード*に基づいてアクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-916">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="ccfe7-917">[GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-917">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="ccfe7-918">ユーザーは、`Accept` メソッドを実装し、実行する制約の "順序" を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-918">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="ccfe7-919">この例では、`Accept` ルート値が一致すると、`true` メソッドは `country` を返してアクションが一致することを示します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-919">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="ccfe7-920">この動作は、非属性アクションにフォールバックできる `RouteValueAttribute` とは異なります。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-920">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="ccfe7-921">サンプルでは、`en-US` アクションを定義すると、`fr-FR` のような国コードは `[CountrySpecific(...)]` が適用されていない、より一般的なコントローラーにフォールバックすることが示されています。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-921">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="ccfe7-922">`Order` プロパティは、制約が含まれる "*ステージ*" を決定します。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-922">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="ccfe7-923">アクションの制約は、`Order` に基づいてグループで実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-923">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="ccfe7-924">たとえば、フレームワークで提供されるすべての HTTP メソッド属性は、同じステージで実行されるように、同じ `Order` 値を使います。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-924">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="ccfe7-925">目的のポリシーを実装するため、必要なだけいくつでもステージを使うことができます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-925">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="ccfe7-926">`Order` の値を決めるときは、HTTP メソッドより前に制約を適用する必要があるかどうかを考えます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-926">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="ccfe7-927">小さい値が先に実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccfe7-927">Lower numbers run first.</span></span>

::: moniker-end
