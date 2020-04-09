---
title: ASP.NET Core でのコントローラー アクションへのルーティング
author: rick-anderson
description: ASP.NET Core MVC でルーティング ミドルウェアを使って、受信した要求の URL を照合し、アクションにマップする方法について説明します。
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c63313ec060c5be368fcbd20edf5f0d557046d2e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977219"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="a02d5-103">ASP.NET Core でのコントローラー アクションへのルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="a02d5-104">[ライアン・ノワク](https://github.com/rynowak)、[カーク・ラーキン](https://twitter.com/serpent5)、[リック・アンダーソン](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a02d5-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a02d5-105">ASP.NETコア コントローラは、ルーティング[ミドルウェア](xref:fundamentals/middleware/index)を使用して着信要求の URL を照合し、それらをアクション にマップ[します](#action)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="a02d5-106">ルート テンプレート:</span><span class="sxs-lookup"><span data-stu-id="a02d5-106">Routes templates:</span></span>

* <span data-ttu-id="a02d5-107">スタートアップ コードまたは属性で定義されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="a02d5-108">URL パスと[アクション](#action)の照合方法を説明する :</span><span class="sxs-lookup"><span data-stu-id="a02d5-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="a02d5-109">リンクの URL を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="a02d5-110">生成されたリンクは、通常、応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="a02d5-111">アクションは、[従来のルーティング](#cr)または[属性](#ar)ルーティングのいずれかです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="a02d5-112">コントローラまたは[アクション](#action)にルートを配置すると、そのルートは属性ルーティングになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="a02d5-113">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="a02d5-114">このドキュメント:</span><span class="sxs-lookup"><span data-stu-id="a02d5-114">This document:</span></span>

* <span data-ttu-id="a02d5-115">MVC とルーティングの相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="a02d5-116">標準的な MVC アプリがルーティング機能をどのように利用するかを示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="a02d5-117">両方をカバーします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-117">Covers both:</span></span>
    * <span data-ttu-id="a02d5-118">[従来のルーティングは](#cr)、通常、コントローラとビューで使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="a02d5-119">REST API で使用される*属性ルーティング*。</span><span class="sxs-lookup"><span data-stu-id="a02d5-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="a02d5-120">主に REST API のルーティングに関心がある場合は、「REST API[の属性ルーティング](#ar)」セクションに移動します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="a02d5-121">詳細[なルーティング](xref:fundamentals/routing)の詳細については、「ルーティング」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="a02d5-122">エンドポイント ルーティングと呼ばれる、ASP.NET Core 3.0 で追加された既定のルーティング システムを指します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="a02d5-123">互換性を保つために、以前のバージョンのルーティングでコントローラーを使用することが可能です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="a02d5-124">手順については[、2.2-3.0 の移行ガイド](xref:migration/22-to-30)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="a02d5-125">従来のルーティング システムの参考資料については[、このドキュメントの 2.2 バージョン](xref:mvc/controllers/routing?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="a02d5-126">従来のルートを設定する</span><span class="sxs-lookup"><span data-stu-id="a02d5-126">Set up conventional route</span></span>

<span data-ttu-id="a02d5-127">`Startup.Configure`通常、[従来のルーティング](#crd)を使用する場合は、次のようなコードがあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="a02d5-128">の<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>呼び出し<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>の内部では、単一のルートを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="a02d5-129">単一のルートは`default`ルートという名前です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-129">The single route is named `default` route.</span></span> <span data-ttu-id="a02d5-130">コントローラーとビューを持つほとんどのアプリでは、ルートに似た`default`ルート テンプレートを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="a02d5-131">REST API は[属性ルーティング](#ar)を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="a02d5-132">ルート テンプレート`"{controller=Home}/{action=Index}/{id?}"`:</span><span class="sxs-lookup"><span data-stu-id="a02d5-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="a02d5-133">次のような URL パスに一致します。`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="a02d5-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="a02d5-134">パスをトークン化して`{ controller = Products, action = Details, id = 5 }`ルート値を抽出します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="a02d5-135">ルート値の抽出は、アプリにコントローラー名と`ProductsController`アクションがある場合に一致します`Details`。</span><span class="sxs-lookup"><span data-stu-id="a02d5-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="a02d5-136">`/Products/Details/5`model は、 の`id = 5`値をバインド`id`して、`5`パラメーターを に設定します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="a02d5-137">詳細については、[モデル バインド](xref:mvc/models/model-binding)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="a02d5-138">`{controller=Home}`は`Home`、既定`controller`の として定義されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="a02d5-139">`{action=Index}`は`Index`、既定`action`の として定義されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="a02d5-140">の`?`文字は`{id?}`オプション`id`として定義されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="a02d5-141">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="a02d5-142">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="a02d5-143">URL パス`/`に一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="a02d5-144">ルート値`{ controller = Home, action = Index }`を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="a02d5-145">既定値`controller`の値と`action`、既定値を使用する値。</span><span class="sxs-lookup"><span data-stu-id="a02d5-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="a02d5-146">`id`URL パスに対応するセグメントがないため、値は生成されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="a02d5-147">`/`アクション`HomeController``Index`が存在する場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="a02d5-148">上記のコントローラ定義とルート テンプレートを使用して`HomeController.Index`、アクションは次の URL パスに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="a02d5-149">URL パス`/`は、ルート テンプレート`Home`の既定の`Index`コントローラとアクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="a02d5-150">URL パス`/Home`は、ルート テンプレート`Index`の既定のアクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="a02d5-151">便利なメソッド <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="a02d5-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="a02d5-152">置き換えます：</span><span class="sxs-lookup"><span data-stu-id="a02d5-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a02d5-153">ルーティングは、 および<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>ミドルウェアを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="a02d5-154">コントローラを使用するには:</span><span class="sxs-lookup"><span data-stu-id="a02d5-154">To use controllers:</span></span>

* <span data-ttu-id="a02d5-155">内部<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*>`UseEndpoints`を呼び出して[、属性ルーティング コントローラ](#ar)をマップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="a02d5-156">または<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>を呼び出して、[従来のルーティングされたコントローラを](#cr)マップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="a02d5-157">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-157">Conventional routing</span></span>

<span data-ttu-id="a02d5-158">従来のルーティングは、コントローラとビューで使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="a02d5-159">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="a02d5-160">これは、"*規則ルーティング*" の例です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="a02d5-161">これは、URL パスの*規則*を確立するため、*従来型ルーティング*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="a02d5-162">最初のパス セグメント`{controller=Home}`は、コントローラ名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="a02d5-163">2 番目の`{action=Index}`セグメント は、[アクション](#action)名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="a02d5-164">3 番目の`{id?}`セグメントは、オプション`id`の .</span><span class="sxs-lookup"><span data-stu-id="a02d5-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="a02d5-165">in`?``{id?}`はオプションになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="a02d5-166">`id`は、モデル エンティティにマップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="a02d5-167">この`default`ルートを使用して、URL パス:</span><span class="sxs-lookup"><span data-stu-id="a02d5-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="a02d5-168">`/Products/List`アクションにマップ`ProductsController.List`されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="a02d5-169">`/Blog/Article/17`にマップ`BlogController.Article`し、通常はモデルは`id`パラメータを 17 にバインドします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="a02d5-170">このマッピング:</span><span class="sxs-lookup"><span data-stu-id="a02d5-170">This mapping:</span></span>

* <span data-ttu-id="a02d5-171">は、コントローラ名と[アクション](#action)名**のみに**基づいています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="a02d5-172">名前空間、ソース ファイルの場所、メソッド パラメーターに基づいていません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="a02d5-173">既定のルートで従来のルーティングを使用すると、各アクションの新しい URL パターンを考え出すことなく、アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="a02d5-174">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)スタイルアクションを持つアプリの場合、コントローラー間で URL の整合性を保つ:</span><span class="sxs-lookup"><span data-stu-id="a02d5-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="a02d5-175">コードを簡略化します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-175">Helps simplify the code.</span></span>
* <span data-ttu-id="a02d5-176">UI の予測を容易にします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="a02d5-177">`id`上記のコードは、ルート テンプレートによってオプションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="a02d5-178">アクションは、URL の一部として提供されるオプションの ID なしで実行できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="a02d5-179">一般に`id`、URL から省略すると次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="a02d5-180">`id`は、モデル`0`バインドによって設定されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="a02d5-181">データベースに一致する エンティティが`id == 0`見つかりません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="a02d5-182">[属性ルーティング](#ar)は、一部のアクションに必要な ID を作成し、他のアクションに対しては必要としない詳細な制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="a02d5-183">規則により、ドキュメントには、正しい使用方法`id`で表示される可能性が高い場合などのオプションのパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="a02d5-184">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="a02d5-185">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="a02d5-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="a02d5-186">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="a02d5-187">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="a02d5-188">多くの Web UI アプリに必要なルート テンプレートは、のみです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="a02d5-189">大規模な Web UI アプリの場合、必要な場合は、多くの場合[、領域](#areas)を使用して別のルート。</span><span class="sxs-lookup"><span data-stu-id="a02d5-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="a02d5-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>および<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:</span><span class="sxs-lookup"><span data-stu-id="a02d5-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="a02d5-191">呼び出された順序に基づいて、**エンドポイントに注文**値を自動的に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="a02d5-192">ASP.NET Core 3.0 以降のエンドポイント ルーティング:</span><span class="sxs-lookup"><span data-stu-id="a02d5-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="a02d5-193">ルートの概念を持っていません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="a02d5-194">拡張性の実行に対する順序の保証は提供されませんが、すべてのエンドポイントが一度に処理されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="a02d5-195">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="a02d5-196">[属性ルーティング](#ar)については、このドキュメントで後述します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="a02d5-197">複数の従来のルート</span><span class="sxs-lookup"><span data-stu-id="a02d5-197">Multiple conventional routes</span></span>

<span data-ttu-id="a02d5-198">にコールを追加することで、複数[の従来のルート](#cr)を`UseEndpoints`内部<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>に<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>追加できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="a02d5-199">これにより、複数の規則を定義したり、次のような特定の[アクション](#action)専用の従来のルートを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="a02d5-200">上記`blog`のコードのルートは **、専用の従来のルート**です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="a02d5-201">これは、専用の従来のルートと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="a02d5-202">[これは、従来のルーティングを](#cr)使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="a02d5-203">これは、特定の[アクション](#action)に専用です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="a02d5-204">`action`ルート テンプレート`"blog/{*article}"`にパラメーターとして表示されないの`controller`で、</span><span class="sxs-lookup"><span data-stu-id="a02d5-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="a02d5-205">既定値のみを持つことができます`{ controller = "Blog", action = "Article" }`。</span><span class="sxs-lookup"><span data-stu-id="a02d5-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="a02d5-206">このルートは常に アクション`BlogController.Article`にマップされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="a02d5-207">`/Blog`、、`/Blog/Article`および`/Blog/{any-string}`はブログルートに一致する唯一の URL パスです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="a02d5-208">前の例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-208">The preceding example:</span></span>

* <span data-ttu-id="a02d5-209">`blog`ルートは最初に追加されるため、一致`default`の優先順位はルートよりも高くなります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="a02d5-210">これは、URL の一部として記事名を持つことを一般的な[Slug](https://developer.mozilla.org/docs/Glossary/Slug)スタイル ルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="a02d5-211">ASP.NET Core 3.0 以降では、ルーティングは次の処理を行いません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="a02d5-212">*ルート*と呼ばれる概念を定義します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-212">Define a concept called a *route*.</span></span> <span data-ttu-id="a02d5-213">`UseRouting`は、ミドルウェア パイプラインにルートマッチングを追加します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="a02d5-214">ミドル`UseRouting`ウェアは、アプリで定義されているエンドポイントのセットを調べ、要求に基づいて最適なエンドポイント一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="a02d5-215">または のような<xref:Microsoft.AspNetCore.Routing.IRouteConstraint>拡張の実行順序に関する保証を<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>提供します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="a02d5-216">[ルーティング](xref:fundamentals/routing)の参考資料については、「ルーティング」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="a02d5-217">従来のルーティング順序</span><span class="sxs-lookup"><span data-stu-id="a02d5-217">Conventional routing order</span></span>

<span data-ttu-id="a02d5-218">従来のルーティングは、アプリによって定義されたアクションとコントローラーの組み合わせにのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="a02d5-219">これは、従来のルートが重複する場合を単純化することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="a02d5-220">を<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>使用してルートを追加し<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>、呼び出された順序に基づいて自動的にエンドポイントに注文値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="a02d5-221">以前に表示されたルートからの一致は、優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="a02d5-222">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a02d5-223">一般に、エリアを含むルートは、エリアのないルートよりも具体的であるため、早めに配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="a02d5-224">キャッチされたすべての`{*article}`ルートパラメータを持つ[専用の従来のルート](#dcr)は、ルートを[貪欲](xref:fundamentals/routing#greedy)にしすぎて、他のルートと一致させるURLと一致することを意味します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-224">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="a02d5-225">欲張り一致を防ぐために、後でルート テーブルに欲張りルートを配置します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="a02d5-226">あいまいなアクションの解決</span><span class="sxs-lookup"><span data-stu-id="a02d5-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="a02d5-227">ルーティングを通じて 2 つのエンドポイントが一致する場合、ルーティングは次のいずれかを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="a02d5-228">最適な候補を選択します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-228">Choose the best candidate.</span></span>
* <span data-ttu-id="a02d5-229">例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-229">Throw an exception.</span></span>

<span data-ttu-id="a02d5-230">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="a02d5-231">上記のコントローラは、一致する 2 つのアクションを定義します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="a02d5-232">URL パス`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="a02d5-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="a02d5-233">ルート`{ controller = Products33, action = Edit, id = 17 }`データ :</span><span class="sxs-lookup"><span data-stu-id="a02d5-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="a02d5-234">これは MVC コントローラーの典型的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="a02d5-235">`Edit(int)`は、製品を編集するためのフォームを表示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="a02d5-236">`Edit(int, Product)`を使用して、転記されたフォームを処理します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="a02d5-237">正しいルートを解決するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-237">To resolve the correct route:</span></span>

* <span data-ttu-id="a02d5-238">`Edit(int, Product)`は、要求が HTTP`POST`の場合に選択されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="a02d5-239">`Edit(int)`[は、HTTP 動詞](#verb)が他の何かである場合に選択されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="a02d5-240">`Edit(int)`通常は を`GET`介して 呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="a02d5-241">は<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、`[HttpPost]`要求の HTTP メソッドに基づいて選択できるようにルーティングに提供されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="a02d5-242">より`HttpPostAttribute`良`Edit(int, Product)`い一致を`Edit(int)`作る.</span><span class="sxs-lookup"><span data-stu-id="a02d5-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="a02d5-243">などの`HttpPostAttribute`属性の役割を理解することが重要です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="a02d5-244">他の[HTTP 動詞](#verb)にも同様の属性が定義されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="a02d5-245">[従来のルーティング](#cr)では、アクションがショー フォームの一部である場合、同じアクション名を使用してフォーム ワークフローを送信するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="a02d5-246">たとえば[、2 つの Edit アクション メソッドを調べる](xref:tutorials/first-mvc-app/controller-methods-views#get-post)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="a02d5-247">最適な候補を選択できない場合、複数の<xref:System.Reflection.AmbiguousMatchException>一致したエンドポイントが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="a02d5-248">従来のルート名</span><span class="sxs-lookup"><span data-stu-id="a02d5-248">Conventional route names</span></span>

<span data-ttu-id="a02d5-249">文字列`"blog"`と次`"default"`の例では、従来のルート名です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="a02d5-250">ルート名はルートに論理名を付けます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-250">The route names give the route a logical name.</span></span> <span data-ttu-id="a02d5-251">名前付きルートは、URL 生成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="a02d5-252">名前付きルートを使用すると、ルートの順序付けによって URL の生成が複雑になる可能性がある場合に、URL の作成が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="a02d5-253">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="a02d5-254">ルート名:</span><span class="sxs-lookup"><span data-stu-id="a02d5-254">Route names:</span></span>

* <span data-ttu-id="a02d5-255">URL の一致や要求の処理には影響しません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="a02d5-256">URL 生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-256">Are used only for URL generation.</span></span>

<span data-ttu-id="a02d5-257">ルート名の概念は、ルーティングで[IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)として表されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="a02d5-258">**ルート名**と**エンドポイント名**という用語:</span><span class="sxs-lookup"><span data-stu-id="a02d5-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="a02d5-259">交換可能です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-259">Are interchangeable.</span></span>
* <span data-ttu-id="a02d5-260">ドキュメントとコードで使用される API は、記述されている API によって異なります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="a02d5-261">REST API の属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="a02d5-262">REST API は、属性ルーティングを使用して、操作が HTTP 動詞 で表されるリソースのセットとして、アプリの機能[をモデル化する](#verb)必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="a02d5-263">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="a02d5-264">次`StartUp.Configure`のコードは、REST API の一般的なコードで、次のサンプルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="a02d5-265">上記のコードでは、内部<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*>`UseEndpoints`で呼び出され、属性ルーティング コントローラをマップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="a02d5-266">次の例では</span><span class="sxs-lookup"><span data-stu-id="a02d5-266">In the following example:</span></span>

* <span data-ttu-id="a02d5-267">上記の`Configure`メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="a02d5-268">`HomeController`は、既定の従来のルート`{controller=Home}/{action=Index}/{id?}`が一致するものと同様の一連の URL に一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="a02d5-269">この`HomeController.Index`アクションは、任意の URL パス`/` `/Home`、 `/Home/Index`、、または`/Home/Index/3`に対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="a02d5-270">この例では、属性ルーティングと従来のルーティングとの間のプログラミングの主な違いを強調[しています](#cr)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="a02d5-271">属性ルーティングでは、ルートを指定するためにより多くの入力が必要です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="a02d5-272">従来のデフォルトルートは、ルートをより簡潔に処理します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="a02d5-273">ただし、属性ルーティングでは、各[アクション](#action)に適用するルート テンプレートを正確に制御できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="a02d5-274">属性ルーティングでは、コントローラー名とアクション名は、アクションが一致する役割を果**たしません**。</span><span class="sxs-lookup"><span data-stu-id="a02d5-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="a02d5-275">次の例は、前の例と同じ URL に一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="a02d5-276">次のコードでは、 と`action``controller`のトークン置換を使用しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="a02d5-277">コントローラには、次`[Route("[controller]/[action]")]`のコードが適用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="a02d5-278">上記のコードでは、メソッド`Index`テンプレートの前に、`/`または`~/`ルート テンプレートを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="a02d5-279">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="a02d5-280">ルート テンプレートの選択については、「[ルート テンプレートの優先順位](xref:fundamentals/routing#rtp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="a02d5-281">ルーティングの予約名</span><span class="sxs-lookup"><span data-stu-id="a02d5-281">Reserved routing names</span></span>

<span data-ttu-id="a02d5-282">コントローラーまたは Razor ページを使用する場合、次のキーワードは予約されたルート パラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="a02d5-283">属性`page`ルーティングでルート パラメータとして使用すると、一般的なエラーになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="a02d5-284">そうすることで、URL 生成との一貫性がなく、混乱を招く動作が発生します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="a02d5-285">特別なパラメーター名は、URL 生成操作が Razor ページまたはコントローラーを参照するかどうかを決定するために、URL 生成によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="a02d5-286">HTTP 動詞テンプレート</span><span class="sxs-lookup"><span data-stu-id="a02d5-286">HTTP verb templates</span></span>

<span data-ttu-id="a02d5-287">ASP.NETコアには、次の HTTP 動詞テンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="a02d5-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="a02d5-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="a02d5-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="a02d5-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="a02d5-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="a02d5-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="a02d5-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="a02d5-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="a02d5-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="a02d5-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="a02d5-293">[[Httpパッチ]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="a02d5-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="a02d5-294">工順テンプレート</span><span class="sxs-lookup"><span data-stu-id="a02d5-294">Route templates</span></span>

<span data-ttu-id="a02d5-295">ASP.NET Core には、次のルート テンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="a02d5-296">HTTP[動詞テンプレート](#verb)はすべてルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="a02d5-297">[[ルート]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="a02d5-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="a02d5-298">Http 動詞属性を使用した属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="a02d5-299">次のコントローラを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="a02d5-300">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-300">In the preceding code:</span></span>

* <span data-ttu-id="a02d5-301">各アクションには属性`[HttpGet]`が含まれており、HTTP GET 要求のみに一致するように制約されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="a02d5-302">アクション`GetProduct`にはテンプレートが`"{id}"`含まれるため`id`、コントローラの`"api/[controller]"`テンプレートに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="a02d5-303">メソッド テンプレートは`"api/[controller]/"{id}""`です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="a02d5-304">したがって、このアクションは、フォーム`/api/test2/xyz`、、`/api/test2/123`など`/api/test2/{any string}`に対する GET 要求にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="a02d5-305">アクション`GetIntProduct`にはテンプレートが`"int/{id:int}")`含まれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="a02d5-306">テンプレート`:int`の一部は、`id`整数に変換できる文字列にルート値を制約します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="a02d5-307">GET 要求`/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="a02d5-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="a02d5-308">このアクションと一致しません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="a02d5-309">[404 Not Found エラーを返](https://developer.mozilla.org/docs/Web/HTTP/Status/404)します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="a02d5-310">アクション`GetInt2Product`はテンプレート`{id}`に含まれますが、整数に変換できる値`id`に制約はありません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="a02d5-311">GET 要求`/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="a02d5-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="a02d5-312">このルートに一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-312">Matches this route.</span></span>
  * <span data-ttu-id="a02d5-313">モデル バインドは整数`abc`への変換に失敗します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="a02d5-314">メソッド`id`のパラメーターは整数です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="a02d5-315">モデル バインドが整数への変換`abc`に失敗したため[、400 不正な要求](https://developer.mozilla.org/docs/Web/HTTP/Status/400)を返します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="a02d5-316">属性<xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute>ルーティングでは<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、 、 、<xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>などの<xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="a02d5-317">すべての[HTTP 動詞](#verb)属性は、ルート テンプレートを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="a02d5-318">次の例は、同じルート テンプレートに一致する 2 つのアクションを示しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="a02d5-319">URL パス`/products3`の使用 :</span><span class="sxs-lookup"><span data-stu-id="a02d5-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="a02d5-320">アクション`MyProductsController.ListProducts`は HTTP[動詞](#verb)が`GET`の場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="a02d5-321">アクション`MyProductsController.CreateProduct`は HTTP[動詞](#verb)が`POST`の場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="a02d5-322">REST API を構築する場合、アクションはすべての HTTP メソッドを受`[Route(...)]`け入れるため、アクションメソッドで使用する必要が生じることはまれです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="a02d5-323">API がサポートする内容を正確に示すために、より具体的な[HTTP 動詞属性](#verb)を使用することをおしいます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="a02d5-324">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="a02d5-325">REST API は、属性ルーティングを使用して、操作が HTTP 動詞によって表される一連のリソースとしてアプリの機能をモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="a02d5-326">つまり、同じ論理リソース上の GET や POST などの多くの操作で同じ URL が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="a02d5-327">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="a02d5-328">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="a02d5-329">次の例では、URL`id`パスの一部として必要です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="a02d5-330">アクション`Products2ApiController.GetProduct(int)`:</span><span class="sxs-lookup"><span data-stu-id="a02d5-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="a02d5-331">次のような URL パスで実行されます。`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="a02d5-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="a02d5-332">URL パス`/products2`で実行されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="a02d5-333">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="a02d5-334">詳細については、「 [[使用] 属性を使用してサポートされる要求コンテンツ タイプを定義する](xref:web-api/index#consumes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="a02d5-335">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="a02d5-336">`[ApiController]`の詳細については、「 [ApiController 属性](xref:web-api/index##apicontroller-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="a02d5-337">ルート名</span><span class="sxs-lookup"><span data-stu-id="a02d5-337">Route name</span></span>

<span data-ttu-id="a02d5-338">次のコードでは、 の "`Products_List`ルート名" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="a02d5-339">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="a02d5-340">ルート名:</span><span class="sxs-lookup"><span data-stu-id="a02d5-340">Route names:</span></span>

* <span data-ttu-id="a02d5-341">ルーティングの URL マッチング動作には影響しません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="a02d5-342">URL 生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-342">Are only used for URL generation.</span></span>

<span data-ttu-id="a02d5-343">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="a02d5-344">前のコードと、パラメータをオプション ( )`id``{id?}`として定義する従来の既定のルートと対比します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="a02d5-345">API を正確に指定する機能には、さまざまなアクションへのアクセス`/products`許可`/products/5`やディスパッチなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="a02d5-346">属性ルートの結合</span><span class="sxs-lookup"><span data-stu-id="a02d5-346">Combining attribute routes</span></span>

<span data-ttu-id="a02d5-347">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="a02d5-348">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="a02d5-349">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="a02d5-350">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="a02d5-350">In the preceding example:</span></span>

* <span data-ttu-id="a02d5-351">URL パス`/products`は一致できます`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="a02d5-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="a02d5-352">URL パス`/products/5`は、`ProductsApi.GetProduct(int)`と一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="a02d5-353">これらのアクションはどちらも属性でマーク`GET`されているため、HTTP にのみ一致`[HttpGet]`します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="a02d5-354">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="a02d5-355">次の例では、既定のルートと同様の URL パスのセットに一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="a02d5-356">次の表に、`[Route]`上記のコードの属性を示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="a02d5-357">属性</span><span class="sxs-lookup"><span data-stu-id="a02d5-357">Attribute</span></span>               | <span data-ttu-id="a02d5-358">と組み合わせる`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="a02d5-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="a02d5-359">工順テンプレートを定義します</span><span class="sxs-lookup"><span data-stu-id="a02d5-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="a02d5-360">はい</span><span class="sxs-lookup"><span data-stu-id="a02d5-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="a02d5-361">はい</span><span class="sxs-lookup"><span data-stu-id="a02d5-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="a02d5-362">**いいえ**</span><span class="sxs-lookup"><span data-stu-id="a02d5-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="a02d5-363">はい</span><span class="sxs-lookup"><span data-stu-id="a02d5-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="a02d5-364">属性の工順順序</span><span class="sxs-lookup"><span data-stu-id="a02d5-364">Attribute route order</span></span>

<span data-ttu-id="a02d5-365">ルーティングはツリーを構築し、すべてのエンドポイントに同時に一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="a02d5-366">ルート エントリは、理想的な順序で配置された場合と同様に動作します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="a02d5-367">最も具体的なルートは、より一般的なルートの前に実行する機会があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="a02d5-368">たとえば、属性ルートのような`blog/search/{topic}`属性ルートは、 のような`blog/{*article}`属性ルートよりも具体的です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="a02d5-369">ルート`blog/search/{topic}`の優先順位は、より具体的であるため、既定ではより高くなります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="a02d5-370">[従来のルーティング](#cr)を使用して、開発者は、希望の順序でルートを配置する責任があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="a02d5-371">属性ルートは、プロパティを使用して注文<xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>を設定できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="a02d5-372">フレームワークで提供されるすべての[ルート属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)には、`Order`が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="a02d5-373">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="a02d5-374">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-374">The default order is `0`.</span></span> <span data-ttu-id="a02d5-375">注文を設定しない`Order = -1`ルートの前に、ランを使用してルートを設定する。</span><span class="sxs-lookup"><span data-stu-id="a02d5-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="a02d5-376">デフォルトのルート順序`Order = 1`付け後に実行を使用してルートを設定する。</span><span class="sxs-lookup"><span data-stu-id="a02d5-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="a02d5-377">**Avoid**に依存しないように`Order`します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="a02d5-378">アプリの URL 領域が正しくルーティングするために明示的な順序値を必要とする場合、クライアントにも混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="a02d5-379">一般に、属性ルーティングは URL 照合を使用して正しいルートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="a02d5-380">URL 生成に使用される既定の順序が機能しない場合、通常は`Order`、オーバーライドとしてルート名を使用する方が、プロパティを適用するよりも簡単です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="a02d5-381">両方ともルートマッチング`/home`を定義する次の2つのコントローラを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="a02d5-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="a02d5-382">上記`/home`のコードを要求すると、次のような例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="a02d5-383">ルート`Order`属性の 1 つに追加すると、あいまいさが解決されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="a02d5-384">上記のコードを使用して`/home`、エンドポイント`HomeController.Index`を実行します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="a02d5-385">に行くために、`MyDemoController.MyIndex`要求`/home/MyIndex`.</span><span class="sxs-lookup"><span data-stu-id="a02d5-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="a02d5-386">**注**: </span><span class="sxs-lookup"><span data-stu-id="a02d5-386">**Note**:</span></span>

* <span data-ttu-id="a02d5-387">上記のコードは、例または不適切なルーティング設計です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="a02d5-388">これは、`Order`プロパティを説明するために使用されました。</span><span class="sxs-lookup"><span data-stu-id="a02d5-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="a02d5-389">この`Order`プロパティは、テンプレートを照合できないあいまいさを解決するだけです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="a02d5-390">テンプレートを削除することをお`[Route("Home")]`しだい。</span><span class="sxs-lookup"><span data-stu-id="a02d5-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="a02d5-391">[Razor ページのルート順序については、「Razor ページのルートとアプリの規則: ルートの順序](xref:razor-pages/razor-pages-conventions#route-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="a02d5-392">場合によっては、あいまいなルートを伴う HTTP 500 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="a02d5-393">[ログを](xref:fundamentals/logging/index)使用して、どのエンドポイントが原因`AmbiguousMatchException`となっているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="a02d5-394">ルートテンプレート[コントローラ]でのトークン置換、[アクション]、[エリア]</span><span class="sxs-lookup"><span data-stu-id="a02d5-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="a02d5-395">便宜上、属性ルートは、次のいずれかにトークンを囲むことで予約ルート パラメータのトークンの置換をサポートします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="a02d5-396">四角いブレース:`[]`</span><span class="sxs-lookup"><span data-stu-id="a02d5-396">Square braces: `[]`</span></span>
* <span data-ttu-id="a02d5-397">中かっこ:`{}`</span><span class="sxs-lookup"><span data-stu-id="a02d5-397">Curly braces: `{}`</span></span>

<span data-ttu-id="a02d5-398">トークン`[action]`、、`[area]`および`[controller]`は、ルートが定義されているアクションのアクション名、エリア名、およびコントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="a02d5-399">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="a02d5-400">一致`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="a02d5-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="a02d5-401">一致`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="a02d5-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="a02d5-402">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="a02d5-403">前の例は、次のコードと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="a02d5-404">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="a02d5-405">これは、トークンの置換と組み合わせた強力な機能です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="a02d5-406">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="a02d5-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="a02d5-408">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="a02d5-409"> では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="a02d5-410">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="a02d5-411">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="a02d5-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="a02d5-412">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="a02d5-413">パラメーター トランスフォーマーは <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="a02d5-414">たとえば、カスタム`SlugifyParameterTransformer`パラメータ トランスフォーマは`SubscriptionManagement`ルート値を`subscription-management`に変更します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="a02d5-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="a02d5-416">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="a02d5-417">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="a02d5-418">上記の`ListAll`メソッドは、`/subscription-management/list-all`と一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="a02d5-419">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="a02d5-420">スラグの定義については[、Slug の MDN Web ドキュメント](https://developer.mozilla.org/docs/Glossary/Slug)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="a02d5-421">複数の属性ルート</span><span class="sxs-lookup"><span data-stu-id="a02d5-421">Multiple attribute routes</span></span>

<span data-ttu-id="a02d5-422">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="a02d5-423">これの最も一般的な使用方法は、次の例で示すように、"既定の規則ルート" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="a02d5-424">コントローラに複数のルート属性を配置すると、それぞれがアクションメソッドの各ルート属性と組み合わされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="a02d5-425">すべての[HTTP 動詞](#verb)ルート制約`IActionConstraint`が実装されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="a02d5-426">実装<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>する複数のルート属性がアクションに配置されている場合:</span><span class="sxs-lookup"><span data-stu-id="a02d5-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="a02d5-427">各アクション制約は、コントローラに適用されたルート テンプレートと組み合わされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="a02d5-428">アクションで複数のルートを使用すると便利で強力に思えるかもしれませんが、アプリの URL 空間を基本的かつ適切に定義しておく方が適しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="a02d5-429">既存のクライアントをサポートするために、必要な場合**にのみ**、複数のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="a02d5-430">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="a02d5-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="a02d5-431">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="a02d5-432">上記のコードでは、`[HttpPost("product/{id:int}")]`ルート制約を適用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="a02d5-433">アクション`ProductsController.ShowProduct`は、 のような`/product/3`URL パスによってのみ照合されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="a02d5-434">ルート テンプレート部分`{id:int}`は、そのセグメントを整数のみに制約します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="a02d5-435">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="a02d5-436">カスタム ルート属性を使用して、プロバイダー</span><span class="sxs-lookup"><span data-stu-id="a02d5-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="a02d5-437">すべての[ルート属性](#rt)が実装<xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="a02d5-438">ASP.NETコアランタイム:</span><span class="sxs-lookup"><span data-stu-id="a02d5-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="a02d5-439">アプリの起動時にコントローラー クラスとアクション メソッドの属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="a02d5-440">実装`IRouteTemplateProvider`する属性を使用して、ルートの初期セットを構築します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="a02d5-441">カスタム`IRouteTemplateProvider`ルート属性を定義するために実装します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="a02d5-442">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="a02d5-443">上記の`Get`メソッドは、`Order = 2, Template = api/MyTestApi`を返します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="a02d5-444">アプリケーション モデルを使用して属性ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="a02d5-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="a02d5-445">アプリケーション モデル:</span><span class="sxs-lookup"><span data-stu-id="a02d5-445">The application model:</span></span>

* <span data-ttu-id="a02d5-446">起動時に作成されるオブジェクト モデルです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="a02d5-447">アプリでアクションをルーティングして実行するために ASP.NET Core が使用するすべてのメタデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="a02d5-448">アプリケーション モデルには、ルート属性から収集されたすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="a02d5-449">ルート属性からのデータは、実装によって提供`IRouteTemplateProvider`されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="a02d5-450">規則：</span><span class="sxs-lookup"><span data-stu-id="a02d5-450">Conventions:</span></span>

* <span data-ttu-id="a02d5-451">アプリケーション モデルを変更してルーティングの動作をカスタマイズするために記述できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="a02d5-452">アプリの起動時に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-452">Are read at app startup.</span></span>

<span data-ttu-id="a02d5-453">このセクションでは、アプリケーション モデルを使用したルーティングのカスタマイズの基本的な例を示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="a02d5-454">次のコードでは、ルートがプロジェクトのフォルダ構造とほぼ同じになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="a02d5-455">次のコードは、属性`namespace`ルーティングされているコントローラに規則が適用されないようにします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="a02d5-456">たとえば、次のコントローラはを使用`NamespaceRoutingConvention`しません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a02d5-457">`NamespaceRoutingConvention.Apply` メソッドは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="a02d5-458">コントローラが属性ルーティングの場合は何もしません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="a02d5-459">ベース`namespace``namespace`を削除した後に、 に基づいてコントローラ テンプレートを設定します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="a02d5-460">は`NamespaceRoutingConvention`で適用できます`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="a02d5-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="a02d5-461">たとえば、次のコントローラを考えます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="a02d5-462">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-462">In the preceding code:</span></span>

* <span data-ttu-id="a02d5-463">ベース`namespace`は`My.Application`です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="a02d5-464">上記のコントローラの完全名は です`My.Application.Admin.Controllers.UsersController`。</span><span class="sxs-lookup"><span data-stu-id="a02d5-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="a02d5-465">コントローラ`NamespaceRoutingConvention`テンプレートを に`Admin/Controllers/Users/[action]/{id?`設定します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="a02d5-466">コントローラ`NamespaceRoutingConvention`の属性として適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="a02d5-467">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="a02d5-468">ASP.NET Core アプリは、従来のルーティングと属性ルーティングの使用を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="a02d5-469">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="a02d5-470">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="a02d5-471">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="a02d5-472">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="a02d5-473">コントローラ上**の任意**のルート属性は、コントローラ属性**のすべての**アクションをルーティングします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="a02d5-474">属性ルーティングと従来のルーティングでは、同じルーティング エンジンを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="a02d5-475">URL 生成とアンビエント値</span><span class="sxs-lookup"><span data-stu-id="a02d5-475">URL Generation and ambient values</span></span>

<span data-ttu-id="a02d5-476">アプリは、ルーティング URL 生成機能を使用して、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="a02d5-477">URL を生成すると、URL のハードコーディングが不要になり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="a02d5-478">このセクションでは、MVC で提供される URL 生成機能に焦点を当て、URL 生成の仕組みの基本のみを取り上げる。</span><span class="sxs-lookup"><span data-stu-id="a02d5-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="a02d5-479">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="a02d5-480">インターフェイス<xref:Microsoft.AspNetCore.Mvc.IUrlHelper>は、MVC と URL 生成のためのルーティングの間のインフラストラクチャの基になる要素です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="a02d5-481">インスタンス`IUrlHelper`は、コントローラ、ビュー、`Url`およびビュー コンポーネントのプロパティを通じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="a02d5-482">次の例では、インターフェイス`IUrlHelper`をプロパティを通じて`Controller.Url`使用して、別のアクションへの URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="a02d5-483">アプリが既定の従来のルートを使用している場合、変数の`url`値は URL パス`/UrlGeneration/Destination`文字列 です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="a02d5-484">この URL パスは、次の組み合わせによってルーティングによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="a02d5-485">現在の要求からのルート値 (**アンビエント値**と呼ばれます ) 。</span><span class="sxs-lookup"><span data-stu-id="a02d5-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="a02d5-486">渡`Url.Action`された値とこれらの値をルート テンプレートに代入します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="a02d5-487">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="a02d5-488">値を持たないルート パラメータは、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="a02d5-489">既定値がある場合は、既定値を使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="a02d5-490">省略可能な場合はスキップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-490">Be skipped if it's optional.</span></span> <span data-ttu-id="a02d5-491">たとえば、`id`からルート テンプレート`{controller}/{action}/{id?}`を使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="a02d5-492">必要なルート パラメータに対応する値がない場合、URL 生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="a02d5-493">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="a02d5-494">前の例では、`Url.Action`[従来のルーティング](#cr)を想定しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="a02d5-495">URL の生成は[属性ルーティング](#ar)と同様に機能しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="a02d5-496">従来のルーティングでは、</span><span class="sxs-lookup"><span data-stu-id="a02d5-496">With conventional routing:</span></span>

* <span data-ttu-id="a02d5-497">工順の値は、テンプレートを展開するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="a02d5-498">ルート値は`controller`、`action`通常、そのテンプレートに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="a02d5-499">これは、ルーティングによって一致する URL が規則に従っているためです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="a02d5-500">次の例では、属性ルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="a02d5-501">上記`Source`のコードのアクションによって、 が`custom/url/to/destination`生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="a02d5-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>の代替として ASP.NET Core 3.0`IUrlHelper`で追加されました。</span><span class="sxs-lookup"><span data-stu-id="a02d5-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="a02d5-503">`LinkGenerator`は、類似するが、より柔軟な機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="a02d5-504">各`IUrlHelper`メソッドには、対応するメソッド ファミリ`LinkGenerator`も含まれています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="a02d5-505">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-505">Generating URLs by action name</span></span>

<span data-ttu-id="a02d5-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) [、LinkGenerator.GetPathByAction、](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)および関連するすべてのオーバーロードは、コントローラ名とアクション名を指定してターゲットエンドポイントを生成するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="a02d5-507">を使用`Url.Action`する場合、現在のルート`controller`値`action`は、ランタイムによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="a02d5-508">`controller`の値と`action`は[、アンビエント値と値](#ambient)の両方の一部です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="a02d5-509">メソッド`Url.Action`は常に現在の値を`action`使用`controller`し、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="a02d5-510">ルーティングは、アンビエント値の値を使用して、URL の生成時に提供されなかった情報を入力しようとします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="a02d5-511">アンビエント値`{a}/{b}/{c}/{d}``{ a = Alice, b = Bob, c = Carol, d = David }`のようなルートを考えてみましょう:</span><span class="sxs-lookup"><span data-stu-id="a02d5-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="a02d5-512">ルーティングには、追加の値を含まない URL を生成するのに十分な情報があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="a02d5-513">すべてのルート パラメータに値があるため、ルーティングには十分な情報があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="a02d5-514">値`{ d = Donovan }`が追加された場合:</span><span class="sxs-lookup"><span data-stu-id="a02d5-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="a02d5-515">値`{ d = David }`は無視されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="a02d5-516">生成される URL`Alice/Bob/Carol/Donovan`パスは です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="a02d5-517">**警告**: URL パスは階層的です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="a02d5-518">前の例では、値`{ c = Cheryl }`が追加された場合は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="a02d5-519">両方の値`{ c = Carol, d = David }`は無視されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="a02d5-520">値がなくなり`d`、URL 生成が失敗します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="a02d5-521">URL を`c`生成するために`d`必要な値と の値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="a02d5-522">既定のルート`{controller}/{action}/{id?}`でこの問題が発生すると予想される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="a02d5-523">この問題は、常に明示的`Url.Action`に値`controller`を`action`指定するため、実際にはまれです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="a02d5-524">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)のいくつかのオーバーロードは、ルート値オブジェクトを受け取り、`controller``action`および 以外のルート パラメータの値を提供します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="a02d5-525">ルート値オブジェクトは、 でよく`id`使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="a02d5-526">たとえば、「 `Url.Action("Buy", "Products", new { id = 17 })` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="a02d5-527">ルート値オブジェクト:</span><span class="sxs-lookup"><span data-stu-id="a02d5-527">The route values object:</span></span>

* <span data-ttu-id="a02d5-528">通常、慣例では匿名型のオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="a02d5-529">`IDictionary<>`または[POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="a02d5-530">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="a02d5-531">上記のコードは、`/Products/Buy/17?color=red`を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="a02d5-532">次のコードでは、絶対 URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="a02d5-533">絶対 URL を作成するには、次のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="a02d5-534">を受け入れるオーバーロード`protocol`。</span><span class="sxs-lookup"><span data-stu-id="a02d5-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="a02d5-535">たとえば、上記のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-535">For example, the preceding code.</span></span>
* <span data-ttu-id="a02d5-536">既定で絶対 URI[を生成](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)する。</span><span class="sxs-lookup"><span data-stu-id="a02d5-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="a02d5-537">ルート別の URL の生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-537">Generate URLs by route</span></span>

<span data-ttu-id="a02d5-538">上記のコードでは、コントローラとアクション名を渡して URL を生成する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="a02d5-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="a02d5-539">`IUrlHelper`また、メソッドの[Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)ファミリも提供します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="a02d5-540">これらのメソッドは[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)に似ていますが、ルート値と`action``controller`ルート値に現在の値はコピーしません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="a02d5-541">の最も一般的`Url.RouteUrl`な使用法:</span><span class="sxs-lookup"><span data-stu-id="a02d5-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="a02d5-542">URL を生成するルート名を指定します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="a02d5-543">通常、コントローラー名やアクション名は指定しません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="a02d5-544">次の Razor ファイルは、 への`Destination_Route`HTML リンクを生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="a02d5-545">HTML とカミソリで URL を生成する</span><span class="sxs-lookup"><span data-stu-id="a02d5-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="a02d5-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>には、<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>それぞれ生成するメソッドを提供`<form>`する Html.BeginForm`<a>`と[Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*)が用意されています。 [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*)</span><span class="sxs-lookup"><span data-stu-id="a02d5-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="a02d5-547">これらのメソッドは[、Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)メソッドを使用して URL を生成し、同様の引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="a02d5-548">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="a02d5-549">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="a02d5-550">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="a02d5-551">詳細については、「[フォームのタグ ヘルパー](xref:mvc/views/working-with-forms) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="a02d5-552">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="a02d5-553">アクション結果の URL 生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-553">URL generation in Action Results</span></span>

<span data-ttu-id="a02d5-554">上記の例は、コントローラ`IUrlHelper`での使用を示しました。</span><span class="sxs-lookup"><span data-stu-id="a02d5-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="a02d5-555">コントローラで最もよく使用される用途は、アクション結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="a02d5-556">基底クラス <xref:Microsoft.AspNetCore.Mvc.ControllerBase> および <xref:Microsoft.AspNetCore.Mvc.Controller> では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="a02d5-557">一般的な使用法の 1 つは、ユーザー入力を受け入れた後にリダイレクトすることです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="a02d5-558">アクションは、ファクトリ メソッドなどの<xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*>メソッド<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>を生成し、そのメソッドと同様`IUrlHelper`のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="a02d5-559">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="a02d5-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="a02d5-560">[従来のルーティング](#cr)は専用の[従来](#dcr)ルートと呼ばれる特殊なルート定義を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="a02d5-561">次の例では、名前の付`blog`いたルートは専用の従来のルートです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="a02d5-562">上記のルート定義を使用して`Url.Action("Index", "Home")`、ルートを使用して`/`URL`default`パスを生成しますが、なぜですか?</span><span class="sxs-lookup"><span data-stu-id="a02d5-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="a02d5-563">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="a02d5-564">[専用の従来のルートは、](#dcr)ルートが URL 生成に[強欲](xref:fundamentals/routing#greedy)すぎることを防ぐ対応するルート パラメーターを持たない既定値の特別な動作に依存します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="a02d5-565">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="a02d5-566">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="a02d5-567">URL の`blog`生成は、値`{ controller = Home, action = Index }`が一致`{ controller = Blog, action = Article }`しないため失敗します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="a02d5-568">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="a02d5-569">Areas</span><span class="sxs-lookup"><span data-stu-id="a02d5-569">Areas</span></span>

<span data-ttu-id="a02d5-570">[領域](xref:mvc/controllers/areas)は、関連する機能を別のグループに整理するために使用される MVC 機能です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="a02d5-571">コントローラーアクションのルーティング名前空間。</span><span class="sxs-lookup"><span data-stu-id="a02d5-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="a02d5-572">ビューのフォルダ構造。</span><span class="sxs-lookup"><span data-stu-id="a02d5-572">Folder structure for views.</span></span>

<span data-ttu-id="a02d5-573">エリアを使用すると、アプリは、異なる領域を持つ限り、同じ名前の複数のコントローラーを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="a02d5-574">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="a02d5-575">ここでは、ルーティングがエリアとどのように相互作用するかを説明します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="a02d5-576">[エリア](xref:mvc/controllers/areas)がビューで使用される方法の詳細については、「エリア」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="a02d5-577">次の例では、既定の従来のルートと名前の`area``area``Blog`ルートを使用するように MVC を構成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="a02d5-578">上記のコードでは、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>が呼び出され`"blog_route"`、 が作成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="a02d5-579">2 番目の`"Blog"`パラメータはエリア名です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="a02d5-580">のような`/Manage/Users/AddUser`URL パスを一致`"blog_route"`させると、ルートは`{ area = Blog, controller = Users, action = AddUser }`ルート値 を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="a02d5-581">ルート`area`値は、 の`area`既定値で生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="a02d5-582">作成される`MapAreaControllerRoute`ルートは、次のルートと同じです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="a02d5-583">`MapAreaControllerRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="a02d5-584">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="a02d5-585">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a02d5-586">一般に、エリアを含むルートは、エリアのないルートよりも具体的であるため、早めに配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="a02d5-587">上記の例を使用すると、ルート値`{ area = Blog, controller = Users, action = AddUser }`は次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="a02d5-588">[[エリア]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性は、エリアの一部としてコントローラを表します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="a02d5-589">このコントローラはエリア内`Blog`にあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="a02d5-590">属性のない`[Area]`コントローラは、どのエリアのメンバでもなく、`area`ルーティングによってルート値が提供される場合は一致**しません**。</span><span class="sxs-lookup"><span data-stu-id="a02d5-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="a02d5-591">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="a02d5-592">完全な処理のために、各コントローラの名前空間をここに示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="a02d5-593">上記のコントローラが同じ名前空間を使用している場合、コンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="a02d5-594">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="a02d5-595">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="a02d5-596">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="a02d5-597">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="a02d5-598">エリア内でアクションを実行する場合、ルート値`area`は URL 生成に使用するルーティングの[アンビエント値](#ambient)として使用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="a02d5-599">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="a02d5-600">次のコードは、 への`/Zebra/Users/AddUser`URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="a02d5-601">アクション定義</span><span class="sxs-lookup"><span data-stu-id="a02d5-601">Action definition</span></span>

<span data-ttu-id="a02d5-602">[NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性を持つメソッドを除く、コントローラー上のパブリック メソッドはアクションです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="a02d5-603">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="a02d5-603">Sample code</span></span>

 * <span data-ttu-id="a02d5-604">[MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="a02d5-605">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a02d5-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a02d5-606">ASP.NET Core MVC は、ルーティング [ミドルウェア](xref:fundamentals/middleware/index)を使って、受信した要求の URL を照合し、アクションにマップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="a02d5-607">ルートは、スタートアップ コードまたは属性で定義されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="a02d5-608">ルートでは、URL パスとアクションの照合方法が記述されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="a02d5-609">また、ルートは、応答で送信される (リンク用) の URL の生成にも使われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="a02d5-610">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="a02d5-611">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="a02d5-612">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="a02d5-613">このドキュメントでは、MVC とルーティングの間の相互作用、および標準的な MVC アプリでのルーティング機能の利用方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="a02d5-614">高度なルーティングについて詳しくは、「[ASP.NET Core のルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="a02d5-615">ルーティング ミドルウェアの設定</span><span class="sxs-lookup"><span data-stu-id="a02d5-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="a02d5-616">*Configure* メソッドには、次のようなコードが含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a02d5-617">`UseMvc` の呼び出しの内部で、`MapRoute` は単一のルートの作成に使われます。これは、`default` ルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="a02d5-618">ほとんどの MVC アプリは、`default` ルートのようなルートをテンプレートで使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="a02d5-619">ルート テンプレート `"{controller=Home}/{action=Index}/{id?}"` は、`/Products/Details/5` のような URL パスと一致し、パスをトークン化することによってルート値 `{ controller = Products, action = Details, id = 5 }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="a02d5-620">MVC は、`ProductsController` という名前のコントローラーを探して、アクション `Details` の実行を試みます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="a02d5-621">この例では、このアクションを呼び出すときに、モデル バインドが `id = 5` という値を使って、`id` パラメーターを `5` に設定することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="a02d5-622">詳しくは、「[モデル バインド](../models/model-binding.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="a02d5-623">`default` ルートの使用:</span><span class="sxs-lookup"><span data-stu-id="a02d5-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a02d5-624">ルート テンプレート:</span><span class="sxs-lookup"><span data-stu-id="a02d5-624">The route template:</span></span>

* <span data-ttu-id="a02d5-625">`{controller=Home}` は、`Home` を既定の `controller` として定義します</span><span class="sxs-lookup"><span data-stu-id="a02d5-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="a02d5-626">`{action=Index}` は、`Index` を既定の `action` として定義します</span><span class="sxs-lookup"><span data-stu-id="a02d5-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="a02d5-627">`{id?}` は、`id` を省略可能として定義します</span><span class="sxs-lookup"><span data-stu-id="a02d5-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="a02d5-628">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="a02d5-629">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="a02d5-630">`"{controller=Home}/{action=Index}/{id?}"` は URL パス `/` と一致でき、ルート値 `{ controller = Home, action = Index }` を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="a02d5-631">`controller` と `action` の値は既定値を使い、`id` は URL パスに対応するセグメントが存在しないため値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="a02d5-632">MVC はこれらのルート値を使って、`HomeController` および `Index` アクションを選びます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="a02d5-633">このコントローラー定義とルート テンプレートを使うと、`HomeController.Index` アクションは次のいずれかの URL パスに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="a02d5-634">便利なメソッド `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="a02d5-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="a02d5-635">これは、次の代わりに使うことができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a02d5-636">`UseMvc` および `UseMvcWithDefaultRoute` は、`RouterMiddleware` のインスタンスをミドルウェア パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="a02d5-637">MVC は、ミドルウェアと直接相互作用せず、ルーティングを使って要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="a02d5-638">MVC は、`MvcRouteHandler` のインスタンスによってルートに接続されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="a02d5-639">`UseMvc` の内部のコードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="a02d5-640">`UseMvc` は、ルートを直接定義することはなく、`attribute` ルートのルート コレクションにプレースホルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="a02d5-641">オーバーロード `UseMvc(Action<IRouteBuilder>)` は、独自ルートの追加を可能にし、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="a02d5-642">`UseMvc` とそのすべてのバリエーションは、属性ルートのプレースホルダーを追加します。属性ルーティングは、`UseMvc` の構成方法に関係なく常に利用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="a02d5-643">`UseMvcWithDefaultRoute` は既定のルートを定義し、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="a02d5-644">「[属性ルーティング](#attribute-routing-ref-label)」セクションでは、属性ルーティングについてさらに詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="a02d5-645">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-645">Conventional routing</span></span>

<span data-ttu-id="a02d5-646">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a02d5-647">上記のコードは、従来のルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="a02d5-648">このスタイルは、URL パスの*規則*を確立するため、従来型ルーティングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="a02d5-649">最初のパス セグメントは、コントローラ名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="a02d5-650">2 つ目はアクション名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-650">The second maps to the action name.</span></span>
* <span data-ttu-id="a02d5-651">3 番目のセグメントは、省略`id`可能な .</span><span class="sxs-lookup"><span data-stu-id="a02d5-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="a02d5-652">`id`はモデル エンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="a02d5-653">この `default` ルートを使うと、URL パス `/Products/List` は `ProductsController.List` アクションにマップし、`/Blog/Article/17` は `BlogController.Article` にマップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="a02d5-654">このマッピングは、コントローラーとアクションの名前に**のみ**基づき、名前空間、ソース ファイルの場所、またはメソッドのパラメーターには基づきません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="a02d5-655">既定のルートで規則ルーティングを使うと、定義するアクションごとに新しい URL パターンを考える必要がなく、アプリケーションをすばやく構築できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="a02d5-656">CRUD スタイルのアクションを使うアプリケーションでは、コントローラー間で URL に一貫性を持たせると、コードが容易になり、UI の予測可能性が向上します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="a02d5-657">`id` はルート テンプレートで省略可能と定義されており、これは URL の一部として ID を提供されなくてもアクションを実行できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="a02d5-658">通常、`id` が URL から省略されると、ID はモデル バインドによって `0` に設定され、結果として、`id == 0` と一致するエンティティはデータベースで検索されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="a02d5-659">属性ルーティングを使うと、ID が必須のアクションと必須ではないアクションをきめ細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="a02d5-660">慣例に従って、ドキュメントでは `id` などの省略可能なパラメーターが正しい使用法で使われる可能性がある場合はパラメーターを記載してあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="a02d5-661">複数のルート</span><span class="sxs-lookup"><span data-stu-id="a02d5-661">Multiple routes</span></span>

<span data-ttu-id="a02d5-662">`MapRoute` の呼び出しをさらに追加することで、`UseMvc` の内部に複数のルートを追加できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="a02d5-663">これにより、次のように、複数の規則を定義すること、または特定のアクションに専用の規則ルートを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a02d5-664">`blog` ルートは "*専用の規則ルート*" です。これは、このルートは規則ルーティング システムを使いますが、特定のアクション専用であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="a02d5-665">`controller` と `action` はパラメーターとしてルート テンプレートに表示されないので、既定値を持つことだけができ、したがってこのルートは常に `BlogController.Article` アクションにマップされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="a02d5-666">ルート コレクション内のルートには順序があり、追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="a02d5-667">そのため、この例では、`blog` ルートが `default` ルートより先に試されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="a02d5-668">*専用の従来のルートでは、URL*パスの残`{*article}`りの部分をキャプチャするなどの**キャッチオール**ルート パラメータを使用することがよくあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="a02d5-669">このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="a02d5-670">この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="a02d5-671">フォールバック</span><span class="sxs-lookup"><span data-stu-id="a02d5-671">Fallback</span></span>

<span data-ttu-id="a02d5-672">要求処理の一環として、MVC はルートの値を使ってアプリケーション内のコントローラーとアクションを検索できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="a02d5-673">ルートの値がアクションと一致しない場合、そのルートは一致と見なされず、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="a02d5-674">これは "*フォールバック*" と呼ばれ、規則ルートがオーバーラップしている場合の簡略化を意図したものです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="a02d5-675">アクションの明確化</span><span class="sxs-lookup"><span data-stu-id="a02d5-675">Disambiguating actions</span></span>

<span data-ttu-id="a02d5-676">2 つのアクションがルーティングで一致する場合、MVC はあいまいさを解消して "最善の" 候補を選ぶか、または例外をスローする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="a02d5-677">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="a02d5-678">このコントローラーでは、URL パス `/Products/Edit/17` およびルート データ `{ controller = Products, action = Edit, id = 17 }` と一致する 2 つのアクションが定義されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="a02d5-679">これは、`Edit(int)` で製品を編集するためのフォームを表示し、`Edit(int, Product)` で送信されたフォームを処理する MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="a02d5-680">これを MVC で可能にするには、要求が HTTP `POST` の場合は `Edit(int, Product)` を選び、それ以外の HTTP 動詞の場合は `Edit(int)` を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="a02d5-681">`HttpPostAttribute` (`[HttpPost]`) は、HTTP 動詞が `POST` の場合にのみそのアクションの選択を許可する `IActionConstraint` の実装です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="a02d5-682">`IActionConstraint` が存在すると、`Edit(int, Product)` の方が `Edit(int)` より "良い" 一致になるので、`Edit(int, Product)` が最初に試されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="a02d5-683">カスタム `IActionConstraint` を作成する必要があるのは特殊なシナリオだけですが、`HttpPostAttribute` のような属性の役割を理解しておくことが重要です。似た属性が他の HTTP 動詞に対しても定義されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="a02d5-684">規則ルーティングでは、アクションが `show form -> submit form` ワークフローの一部になっている場合、複数のアクションが同じアクション名を使うのはよくあることです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="a02d5-685">「[IActionConstraint について](#understanding-iactionconstraint)」セクションを読むと、このパターンの便利さがいっそうよくわかります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="a02d5-686">複数のルートが一致し、MVC が "最善の " ルートを発見できない場合、MVC は `AmbiguousActionException` をスローします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="a02d5-687">ルート名</span><span class="sxs-lookup"><span data-stu-id="a02d5-687">Route names</span></span>

<span data-ttu-id="a02d5-688">次の例の文字列 `"blog"` と `"default"` は、ルート名です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a02d5-689">ルート名は、URL の生成に名前付きのルートを使うことができるよう、ルートに論理名を提供します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="a02d5-690">これは、ルートの順序指定によって URL の生成が複雑になる場合に、URL の作成を大幅に合理化します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="a02d5-691">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="a02d5-692">ルート名が URL の照合や要求の処理に影響を与えることはありません。ルート名は URL の生成のみに使われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="a02d5-693">MVC 固有のヘルパーでの URL の生成など、URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="a02d5-694">属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-694">Attribute routing</span></span>

<span data-ttu-id="a02d5-695">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="a02d5-696">次の例では、`app.UseMvc();` が `Configure` メソッド内で使われており、ルートは渡されていません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="a02d5-697">`HomeController` は、既定のルート `{controller=Home}/{action=Index}/{id?}` が一致するのと同じように、URL のセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="a02d5-698">`HomeController.Index()` アクションは、URL パス `/`、`/Home`、または `/Home/Index` のいずれかに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="a02d5-699">この例では、属性ルーティングと規則ルーティングでのプログラミングの大きな違いが強調して示されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="a02d5-700">属性ルーティングの方が、ルートを指定するために多くの入力を必要とします。既定の規則ルートの方が簡潔にルートを処理します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="a02d5-701">ただし、属性ルーティングでは、各アクションに適用するルート テンプレートを正確に制御できます (そして制御する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="a02d5-702">属性ルーティングでは、コントローラー名とアクション名はアクションの選択において役割を**持ちません**。</span><span class="sxs-lookup"><span data-stu-id="a02d5-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="a02d5-703">この例では、前の例と同じ URL を照合します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-703">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="a02d5-704">上のルート テンプレートでは、`action`、`area`、`controller` のルート パラメーターは定義されていません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="a02d5-705">実際、これらのルート パラメーターは属性ルートでは許可されていません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="a02d5-706">ルート テンプレートはアクションと既に関連付けられているため、URL からアクション名を解析しても意味はありません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="a02d5-707">Http[Verb] 属性を使用する属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="a02d5-708">属性ルーティングでは、`HttpPostAttribute` などの `Http[Verb]` 属性も使うことができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="a02d5-709">これらの属性はすべて、ルート テンプレートを受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="a02d5-710">次の例では、同じルート テンプレートと一致する 2 つのアクションが示されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-710">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="a02d5-711">`/products` のような URL パスの場合、HTTP 動詞が `GET` のときは `ProductsApi.ListProducts` アクションが実行され、HTTP 動詞が `POST` のときは `ProductsApi.CreateProduct` が実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="a02d5-712">属性ルーティングでは、最初に、ルート属性で定義されているルート テンプレートのセットに対して URL が照合されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="a02d5-713">ルート テンプレートが一致すると、`IActionConstraint` 制約が適用されて、実行できるアクションが決定されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="a02d5-714">REST API を構築する場合、アクションではすべての HTTP メソッドが受け入れられるので、アクション メソッドに対して `[Route(...)]` を使用することはまれです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="a02d5-715">より固有の `Http*Verb*Attributes` を使って、API がサポートするものを正確に指定するのがよい方法です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="a02d5-716">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="a02d5-717">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="a02d5-718">次の例では、`id` は URL パスの一部として必須です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="a02d5-719">`ProductsApi.GetProduct(int)` アクションは、`/products/3` のような URL パスに対しては実行されますが、`/products` のような URL パスに対しては実行されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="a02d5-720">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="a02d5-721">ルート名</span><span class="sxs-lookup"><span data-stu-id="a02d5-721">Route Name</span></span>

<span data-ttu-id="a02d5-722">次のコードでは、`Products_List` の "*ルート名*" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="a02d5-723">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="a02d5-724">ルート名は、ルーティングの URL 照合動作には影響を与えず、URL 生成に対してのみ使われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="a02d5-725">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="a02d5-726">これに対し、規則 "*既定ルート*" では、`id` パラメーターは省略可能 (`{id?}`) として定義されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="a02d5-727">API を厳密に指定するこの機能には、`/products` と `/products/5` を異なるアクションに対してディスパッチできるといった利点があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="a02d5-728">ルートの結合</span><span class="sxs-lookup"><span data-stu-id="a02d5-728">Combining routes</span></span>

<span data-ttu-id="a02d5-729">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="a02d5-730">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="a02d5-731">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="a02d5-732">次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="a02d5-733">どちらのアクションも、`HttpGetAttribute` でマークされているため、HTTP `GET` だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="a02d5-734">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="a02d5-735">次の例は、"*既定ルート*" と同様の URL パスのセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-735">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="a02d5-736">属性ルートの順序の指定</span><span class="sxs-lookup"><span data-stu-id="a02d5-736">Ordering attribute routes</span></span>

<span data-ttu-id="a02d5-737">定義された順序で実行される従来のルートとは対照的に、属性ルーティングはツリーを構築し、すべてのルートを同時に照合します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="a02d5-738">これは、ルート エントリが理想的な順序で配置されているかのように動作します。一般的なルートより前に、最も固有のルートが実行する機会があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="a02d5-739">たとえば、`blog/search/{topic}` のようなルートは、`blog/{*article}` のようなルートより固有です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="a02d5-740">論理的には、`blog/search/{topic}` ルートが唯一の意味のある順序なので、既定では、これが最初に "実行" されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="a02d5-741">規則ルーティングを使うときは、開発者が目的の順序でルートを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="a02d5-742">属性ルートでは、フレームワークが提供するすべてのルート属性の `Order` プロパティを使って、順序を構成できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="a02d5-743">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="a02d5-744">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-744">The default order is `0`.</span></span> <span data-ttu-id="a02d5-745">`Order = -1` を使ってルートを設定すると、順序が設定されていないルートの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="a02d5-746">`Order = 1` を使ってルートを設定すると、既定のルート順序の後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="a02d5-747">`Order` には依存しないでください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="a02d5-748">URL 空間で正しくルーティングするために明示的な順序値が必要な場合、クライアントの混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="a02d5-749">一般に、属性ルーティングは URL 照合で正しいルートを選びます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="a02d5-750">URL の生成に使われる既定の順序がうまくいかない場合は、通常、オーバーライドとしてルート名を使う方が、`Order` プロパティを適用するより簡単です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="a02d5-751">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="a02d5-752">Razor Pages でのルート順序に関する情報は、[Razor Pages のルートとアプリの規則:ルート順序](xref:razor-pages/razor-pages-conventions#route-order)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="a02d5-753">ルート テンプレートでのトークンの置換 ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="a02d5-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="a02d5-754">便宜上、属性ルートは *、トークン*を角かっこ (`[`, )`]`で囲むことでトークンの置換をサポートします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="a02d5-755">トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="a02d5-756">次の例では、アクションはコメントで説明されているように URL パスと一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="a02d5-757">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="a02d5-758">上の例は、次のコードと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="a02d5-759">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="a02d5-760">これをトークンの置換と組み合わせると特に強力です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-760">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="a02d5-761">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="a02d5-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="a02d5-763">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="a02d5-764">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="a02d5-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="a02d5-765">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="a02d5-766">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="a02d5-767">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="a02d5-768">`RouteTokenTransformerConvention` は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="a02d5-769">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="a02d5-770">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="a02d5-771">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="a02d5-772">複数のルート</span><span class="sxs-lookup"><span data-stu-id="a02d5-772">Multiple Routes</span></span>

<span data-ttu-id="a02d5-773">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="a02d5-774">これの最も一般的な使用方法は、次の例で示すように、"*既定の規則ルート*" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="a02d5-775">コントローラーに複数のルート属性を配置すると、それぞれが、アクション メソッドの各ルート属性と結合します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="a02d5-776">アクションに (`IActionConstraint` を実装する) 複数のルート属性を配置すると、各アクション制約がそれを定義した属性のルート テンプレートと結合します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="a02d5-777">アクションで複数のルートを使うと強力に見えますが、アプリケーションの URL 空間は簡潔で明確に定義された状態に保つことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="a02d5-778">アクションで複数のルートを使うのは、必要な場合 (既存のクライアントをサポートする、など) だけにしてください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="a02d5-779">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="a02d5-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="a02d5-780">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="a02d5-781">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="a02d5-782">`IRouteTemplateProvider` を使用するカスタム ルート属性</span><span class="sxs-lookup"><span data-stu-id="a02d5-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="a02d5-783">フレームワークで提供されるすべてのルート属性 (`[Route(...)]`、`[HttpGet(...)]` など) は、`IRouteTemplateProvider` インターフェイスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="a02d5-784">アプリが起動し、`IRouteTemplateProvider` を実装している属性を使ってルートの初期セットを作成すると、MVC はコントローラー クラスとアクション メソッドで属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="a02d5-785">`IRouteTemplateProvider` を実装して、独自のルート属性を定義できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="a02d5-786">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="a02d5-787">上の例の属性は、`[MyApiController]` が適用されると、自動的に `Template` を `"api/[controller]"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="a02d5-788">アプリケーション モデルを使用した属性ルートのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="a02d5-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="a02d5-789">"*アプリケーション モデル*" は、アクションをルーティングおよび実行するために MVC によって使われるすべてのメタデータで起動時に作成されるオブジェクト モデルです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="a02d5-790">"*アプリケーション モデル*" には、(`IRouteTemplateProvider` によって) ルート属性から収集されるすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="a02d5-791">起動時にアプリケーション モデルを変更してルーティングの動作方法をカスタマイズする "*規則*" を作成できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="a02d5-792">このセクションでは、アプリケーション モデルを使ってルーティングをカスタマイズする簡単な例を示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="a02d5-793">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="a02d5-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="a02d5-794">MVC アプリケーションでは、規則ルーティングと属性ルーティングを併用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="a02d5-795">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="a02d5-796">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="a02d5-797">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="a02d5-798">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="a02d5-799">コントローラーの**任意**のルート属性により、コントローラー属性のすべてのアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="a02d5-800">2 種類のルーティング システムを区別しているのは、URL がルート テンプレートと一致した後に適用されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="a02d5-801">規則ルーティングでは、一致のルート値を使って、規則ルーティングされるすべてのアクションの参照テーブルから、アクションとコントローラーが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="a02d5-802">属性ルーティングでは、各テンプレートはアクションと既に関連付けられており、それ以上参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="a02d5-803">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="a02d5-803">Complex segments</span></span>

<span data-ttu-id="a02d5-804">複雑なセグメント (たとえば、`[Route("/dog{token}cat")]`) は、右から左にリテラルを最短の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="a02d5-805">説明については、[ソース コード](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="a02d5-806">詳しくは、[こちらの懸案事項](https://github.com/dotnet/AspNetCore.Docs/issues/8197)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="a02d5-807">URL の生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-807">URL Generation</span></span>

<span data-ttu-id="a02d5-808">MVC アプリケーションでは、ルーティングの URL 生成機能を使って、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="a02d5-809">URL を生成すると URL をハードコーディングする必要がなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="a02d5-810">このセクションでは、MVC によって提供される URL 生成機能について説明します。URL 生成のしくみに関する基本だけを取り上げます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="a02d5-811">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="a02d5-812">`IUrlHelper` インターフェイスは、MVC と URL 生成のルーティングの間にあるインフラストラクチャの基盤部分です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="a02d5-813">使用可能な `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビュー コンポーネントの `Url` プロパティを使って探します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="a02d5-814">この例の `IUrlHelper` インターフェイスは、別のアクションへの URL を生成するために `Controller.Url` プロパティを介して使われています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="a02d5-815">アプリケーションが既定の規則ルートを使っている場合、`url` 変数の値は URL パス文字列 `/UrlGeneration/Destination` になります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="a02d5-816">この URL パスは、ルーティングにより、現在の要求からのルート値 (アンビエント値) と、`Url.Action` に渡された値を結合し、これらの値でルート テンプレートを置換することによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="a02d5-817">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="a02d5-818">値を持たないルート パラメーターは、既定値がある場合はそれを使うことができ、省略可能な場合はスキップできます (この例の `id` の場合と同様)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="a02d5-819">必須のルート パラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="a02d5-820">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="a02d5-821">上の `Url.Action` の例では規則ルーティングを想定しています。URL 生成は属性ルーティングでも同じように動作しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="a02d5-822">規則ルーティングでは、ルート値を使ってテンプレートが展開され、通常、`controller` と `action` のルートがそのテンプレートに表示されます。これは、ルーティングによって一致した URL が "*規則*" に従うために動作します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="a02d5-823">属性ルーティングでは、`controller` と `action` のルート値はテンプレートに表示できません。代わりに、使うテンプレートの検索に使われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="a02d5-824">この例では、属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="a02d5-825">MVC はすべての属性ルーティングされるアクションの参照テーブルを作成し、`controller` と `action` の値で照合して、URL 生成に使うルート テンプレートを選びます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="a02d5-826">上の例では、`custom/url/to/destination` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="a02d5-827">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-827">Generating URLs by action name</span></span>

<span data-ttu-id="a02d5-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="a02d5-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="a02d5-829">`Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="a02d5-830">`Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"\*アンビエント値" \* **と "** *値*" 両方の一部です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="a02d5-831">`Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="a02d5-832">ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="a02d5-833">`{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="a02d5-834">値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="a02d5-835">URL パスは階層的です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-835">URL paths are hierarchical.</span></span> <span data-ttu-id="a02d5-836">上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="a02d5-837">この場合、`d` には値がなくなり、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="a02d5-838">`c` および `d` の目的の値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="a02d5-839">既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="a02d5-840">`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="a02d5-841">これを最もよく目にするのは、`Url.Action("Buy", "Products", new { id = 17 })` のように `id` で使われる場合です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="a02d5-842">慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="a02d5-843">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="a02d5-844">絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="a02d5-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="a02d5-845">ルートによる URL の生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-845">Generating URLs by route</span></span>

<span data-ttu-id="a02d5-846">上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。</span><span class="sxs-lookup"><span data-stu-id="a02d5-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="a02d5-847">`IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="a02d5-848">これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="a02d5-849">最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。</span><span class="sxs-lookup"><span data-stu-id="a02d5-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="a02d5-850">HTML での URL の生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-850">Generating URLs in HTML</span></span>

<span data-ttu-id="a02d5-851">`IHtmlHelper` で提供されている `HtmlHelper` メソッドの `Html.BeginForm` および `Html.ActionLink` は、それぞれ `<form>` 要素と `<a>` 要素を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="a02d5-852">これらのメソッドは `Url.Action` メソッドを使って URL を生成するので、同じような引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="a02d5-853">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="a02d5-854">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="a02d5-855">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="a02d5-856">詳しくは、「[フォームの操作](../views/working-with-forms.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="a02d5-857">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="a02d5-858">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="a02d5-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="a02d5-859">上の例ではコントローラーでの `IUrlHelper` の使用が示されていますが、コントローラーでの最も一般的な使用方法はアクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="a02d5-860">基底クラス `ControllerBase` および `Controller` では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="a02d5-861">一般的な使用法の 1 つは、ユーザー入力を受け付けた後でリダイレクトする場合です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-861">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="a02d5-862">アクション結果ファクトリ メソッドは、`IUrlHelper` のメソッドに似たパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="a02d5-863">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="a02d5-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="a02d5-864">規則ルーティングでは、"*専用規則ルート*" と呼ばれる特殊なルート定義を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="a02d5-865">次の例の `blog` という名前のルートが専用規則ルートです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a02d5-866">これらのルート定義を使うと、`Url.Action("Index", "Home")` は `default` ルートで URL パス `/` を生成します。なぜでしょうか。</span><span class="sxs-lookup"><span data-stu-id="a02d5-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="a02d5-867">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="a02d5-868">専用規則ルートは、URL の生成でルートの "一致範囲が広くなりすぎる" のを防ぐために対応するルート パラメーターを持たない既定値の特別な動作に依存します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="a02d5-869">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="a02d5-870">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="a02d5-871">値 `{ controller = Home, action = Index }` は `{ controller = Blog, action = Article }` と一致しないため、`blog` を使った URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="a02d5-872">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="a02d5-873">Areas</span><span class="sxs-lookup"><span data-stu-id="a02d5-873">Areas</span></span>

<span data-ttu-id="a02d5-874">[区分](areas.md)は MVC の機能であり、関連する機能を独立したルーティング名前空間 (コントローラー アクションの場合) およびフォルダー構造 (ビューの場合) としてグループ化するために使われます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="a02d5-875">区分を使うと、アプリケーションは同じ名前の複数のコントローラーを持つことができます (ただし、"*区分*" が異なる場合)。</span><span class="sxs-lookup"><span data-stu-id="a02d5-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="a02d5-876">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="a02d5-877">このセクションでは、ルーティングと区分がどのように相互作用するのかについて説明します。ビューでの区分の使い方について詳しくは、「[区分](areas.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="a02d5-878">次の例では、既定の規則ルートと、`Blog` という名前の区分に対する "*区分ルート*" を使うように、MVC を構成しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="a02d5-879">`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="a02d5-880">`area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="a02d5-881">`MapAreaRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="a02d5-882">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="a02d5-883">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a02d5-884">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="a02d5-885">上の例を使うと、ルート値は次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="a02d5-886">`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="a02d5-887">`[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、`area` ルート値がルーティングによって提供されたときは一致**しません**。</span><span class="sxs-lookup"><span data-stu-id="a02d5-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="a02d5-888">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="a02d5-889">ここでは完全を期すために各コントローラーの名前空間を示してあります。そうしないと、コントローラーの名前が競合し、コンパイラ エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="a02d5-890">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="a02d5-891">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="a02d5-892">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="a02d5-893">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="a02d5-894">区分の内部でアクションを実行するとき、`area` のルート値は、URL の生成に使うルーティングの "*アンビエント値*" として利用できます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="a02d5-895">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="a02d5-896">IActionConstraint について</span><span class="sxs-lookup"><span data-stu-id="a02d5-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="a02d5-897">このセクションでは、フレームワークの内部構造と MVC が実行するアクションを選ぶ方法について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="a02d5-898">一般的なアプリケーションでは、カスタム `IActionConstraint` は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a02d5-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="a02d5-899">インターフェイスにあまり詳しくない場合でも、既に `IActionConstraint` を使ったことがあるはずです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="a02d5-900">`[HttpGet]` 属性およびそれに似た `[Http-VERB]` 属性は、アクション メソッドの実行を制限するために `IActionConstraint` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="a02d5-901">既定の規則ルートでは、URL パス `/Products/Edit` は値 `{ controller = Products, action = Edit }` を生成し、ここで示す**両方**のアクションと一致するものとします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="a02d5-902">`IActionConstraint` ではこれを、両方のアクションが候補と見なされる、といいます。どちらもルート データと一致するからです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="a02d5-903">`HttpGetAttribute` は実行すると、*Edit()* は *GET* に対して一致し、他の HTTP 動詞には一致しないことを示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="a02d5-904">`Edit(...)` アクションには制約が定義されていないので、すべての HTTP 動詞と一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="a02d5-905">したがって、`POST` は `Edit(...)` のみと一致します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="a02d5-906">一方、`GET` の場合は両方のアクションが一致します。しかし、`IActionConstraint` を指定されているアクションの方が常に、指定されていないアクション "*より適切*" であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="a02d5-907">したがって、`Edit()` には `[HttpGet]` があるので、より具体的と見なされ、両方のアクションが一致する場合はこちらが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="a02d5-908">概念的には、`IActionConstraint` は "*オーバーロード*" の形式ですが、同じ名前のメソッドをオーバーロードするのではなく、同じ URL に一致するアクション間でオーバーロードします。</span><span class="sxs-lookup"><span data-stu-id="a02d5-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="a02d5-909">属性ルーティングも `IActionConstraint` を使い、異なるコントローラーのアクションがどちらも候補と見なされる結果になることがあります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="a02d5-910">IActionConstraint の実装</span><span class="sxs-lookup"><span data-stu-id="a02d5-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="a02d5-911">`IActionConstraint` を実装する最も簡単な方法は、`System.Attribute` の派生クラスを作成し、アクションとコントローラーに配置する方法です。</span><span class="sxs-lookup"><span data-stu-id="a02d5-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="a02d5-912">MVC は、属性として適用された `IActionConstraint` を自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="a02d5-913">アプリケーション モデルを使って制約を適用することができ、適用方法をメタプログラムできるので、おそらくこれが最も柔軟なアプローチです。</span><span class="sxs-lookup"><span data-stu-id="a02d5-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="a02d5-914">次の例では、制約は、ルート データから*国コード*に基づいてアクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="a02d5-915">[GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a02d5-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="a02d5-916">ユーザーは、`Accept` メソッドを実装し、実行する制約の "順序" を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="a02d5-917">この例では、`country` ルート値が一致すると、`Accept` メソッドは `true` を返してアクションが一致することを示します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="a02d5-918">この動作は、非属性アクションにフォールバックできる `RouteValueAttribute` とは異なります。</span><span class="sxs-lookup"><span data-stu-id="a02d5-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="a02d5-919">サンプルでは、`en-US` アクションを定義すると、`fr-FR` のような国コードは `[CountrySpecific(...)]` が適用されていない、より一般的なコントローラーにフォールバックすることが示されています。</span><span class="sxs-lookup"><span data-stu-id="a02d5-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="a02d5-920">`Order` プロパティは、制約が含まれる "*ステージ*" を決定します。</span><span class="sxs-lookup"><span data-stu-id="a02d5-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="a02d5-921">アクションの制約は、`Order` に基づいてグループで実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="a02d5-922">たとえば、フレームワークで提供されるすべての HTTP メソッド属性は、同じステージで実行されるように、同じ `Order` 値を使います。</span><span class="sxs-lookup"><span data-stu-id="a02d5-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="a02d5-923">目的のポリシーを実装するため、必要なだけいくつでもステージを使うことができます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="a02d5-924">`Order` の値を決めるときは、HTTP メソッドより前に制約を適用する必要があるかどうかを考えます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="a02d5-925">小さい値が先に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a02d5-925">Lower numbers run first.</span></span>

::: moniker-end
