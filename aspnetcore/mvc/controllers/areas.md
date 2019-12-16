---
title: ASP.NET Core の区分
author: rick-anderson
description: 区分は ASP.NET MVC の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用する方法を説明します。
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 7e02a21361e0e2148b29a3ae0f1ba25e68239e13
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881114"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="13552-103">ASP.NET Core の区分</span><span class="sxs-lookup"><span data-stu-id="13552-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="13552-104">作成者: [Dhananjay Kumar](https://twitter.com/debug_mode) および [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="13552-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="13552-105">区分は ASP.NET の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13552-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="13552-106">ルーティングを行うために、区分を使用して、別のルート パラメーター `area` を `controller` と `action` または Razor Page `page` に追加して階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="13552-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="13552-107">区分は、ASP.NET Core Web アプリをより小さな機能グループにパーティション分割する方法であり、分割した各グループにそれぞれの Razor Pages、コントローラー、ビュー、モデルが与えられます。</span><span class="sxs-lookup"><span data-stu-id="13552-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="13552-108">区分は、実質的にはアプリ内の構造体となります。</span><span class="sxs-lookup"><span data-stu-id="13552-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="13552-109">ASP.NET Core Web プロジェクトでは、ページ、モデル、コントローラー、ビューなどの論理コンポーネントが別々のフォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="13552-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="13552-110">ASP.NET Core ランタイムでは、名前付け規則を使用し、これらのコンポーネント間のリレーションシップを作成します。</span><span class="sxs-lookup"><span data-stu-id="13552-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="13552-111">大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="13552-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="13552-112">チェックアウト、請求、検索などの複数のビジネス ユニットがある eコマース アプリの場合です。</span><span class="sxs-lookup"><span data-stu-id="13552-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="13552-113">これらのユニットにはそれぞれ、ビュー、コントローラー、Razor Pages、モデルを含める独自の論理区分があります。</span><span class="sxs-lookup"><span data-stu-id="13552-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="13552-114">次のような場合は、プロジェクトで区分を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="13552-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="13552-115">論理的に大まかに区切れる複数の機能コンポーネントでアプリが構成されている。</span><span class="sxs-lookup"><span data-stu-id="13552-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="13552-116">各機能区分を個別に使用できるようにアプリを分割したい。</span><span class="sxs-lookup"><span data-stu-id="13552-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="13552-117">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="13552-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="13552-118">ダウンロード サンプルからは、区分をテストするための基本的なアプリが与えられます。</span><span class="sxs-lookup"><span data-stu-id="13552-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="13552-119">Razor Pages を使用している場合は、このドキュメントの「[Razor Pages を使った区分](#areas-with-razor-pages)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13552-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="13552-120">ビューを伴うコントローラーの区分</span><span class="sxs-lookup"><span data-stu-id="13552-120">Areas for controllers with views</span></span>

<span data-ttu-id="13552-121">区分、コントローラー、ビューを使用する一般的な ASP.NET Core Web アプリに含まれる内容:</span><span class="sxs-lookup"><span data-stu-id="13552-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="13552-122">[区分フォルダーの構造](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="13552-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="13552-123">コントローラーと区分を関連付けるための [`[Area]`](#attribute) 属性を持つコントローラー:</span><span class="sxs-lookup"><span data-stu-id="13552-123">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="13552-124">[スタートアップに追加された区分ルート](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="13552-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="13552-125">区分フォルダーの構造</span><span class="sxs-lookup"><span data-stu-id="13552-125">Area folder structure</span></span>

<span data-ttu-id="13552-126">あるアプリに *Products* と *Services* という 2 つの論理グループが与えられているとします。</span><span class="sxs-lookup"><span data-stu-id="13552-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="13552-127">区分を利用すると、フォルダーの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="13552-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="13552-128">Project name</span><span class="sxs-lookup"><span data-stu-id="13552-128">Project name</span></span>
  * <span data-ttu-id="13552-129">Areas</span><span class="sxs-lookup"><span data-stu-id="13552-129">Areas</span></span>
    * <span data-ttu-id="13552-130">Products</span><span class="sxs-lookup"><span data-stu-id="13552-130">Products</span></span>
      * <span data-ttu-id="13552-131">Controllers</span><span class="sxs-lookup"><span data-stu-id="13552-131">Controllers</span></span>
        * <span data-ttu-id="13552-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="13552-132">HomeController.cs</span></span>
        * <span data-ttu-id="13552-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="13552-133">ManageController.cs</span></span>
      * <span data-ttu-id="13552-134">Views</span><span class="sxs-lookup"><span data-stu-id="13552-134">Views</span></span>
        * <span data-ttu-id="13552-135">Home</span><span class="sxs-lookup"><span data-stu-id="13552-135">Home</span></span>
          * <span data-ttu-id="13552-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="13552-136">Index.cshtml</span></span>
        * <span data-ttu-id="13552-137">Manage</span><span class="sxs-lookup"><span data-stu-id="13552-137">Manage</span></span>
          * <span data-ttu-id="13552-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="13552-138">Index.cshtml</span></span>
          * <span data-ttu-id="13552-139">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="13552-139">About.cshtml</span></span>
    * <span data-ttu-id="13552-140">サービス</span><span class="sxs-lookup"><span data-stu-id="13552-140">Services</span></span>
      * <span data-ttu-id="13552-141">Controllers</span><span class="sxs-lookup"><span data-stu-id="13552-141">Controllers</span></span>
        * <span data-ttu-id="13552-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="13552-142">HomeController.cs</span></span>
      * <span data-ttu-id="13552-143">Views</span><span class="sxs-lookup"><span data-stu-id="13552-143">Views</span></span>
        * <span data-ttu-id="13552-144">Home</span><span class="sxs-lookup"><span data-stu-id="13552-144">Home</span></span>
          * <span data-ttu-id="13552-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="13552-145">Index.cshtml</span></span>

<span data-ttu-id="13552-146">区分を使用するとき、前述のレイアウトが一般的ですが、このフォルダー構造を使用するにはビュー ファイルのみが求められます。</span><span class="sxs-lookup"><span data-stu-id="13552-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="13552-147">ビューの検出では、一致する区分ビュー ファイルを次の順序で検索します。</span><span class="sxs-lookup"><span data-stu-id="13552-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="13552-148">コントローラーを区分に関連付ける</span><span class="sxs-lookup"><span data-stu-id="13552-148">Associate the controller with an Area</span></span>

<span data-ttu-id="13552-149">区分コントローラーは [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 属性で指名されます。</span><span class="sxs-lookup"><span data-stu-id="13552-149">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="13552-150">区分ルートを追加する</span><span class="sxs-lookup"><span data-stu-id="13552-150">Add Area route</span></span>

<span data-ttu-id="13552-151">区分ルートでは通常、属性ルーティングではなく、従来のルーティングが使用されます。</span><span class="sxs-lookup"><span data-stu-id="13552-151">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="13552-152">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="13552-152">Conventional routing is order-dependent.</span></span> <span data-ttu-id="13552-153">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13552-153">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="13552-154">URL スペースがすべての区分で統一されている場合、ルート テンプレートでトークンとして `{area:...}` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="13552-154">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="13552-155">上記のコードでは、ルートは 1 つの区分に一致しなければならないという制約が `exists` によって適用されます。</span><span class="sxs-lookup"><span data-stu-id="13552-155">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="13552-156">`{area:...}` の使用は、ルーティングを区分に追加するメカニズムとして最も単純です。</span><span class="sxs-lookup"><span data-stu-id="13552-156">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="13552-157">次のコードでは、<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> を使用し、名前の付いた区分ルートが 2 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="13552-157">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="13552-158">ASP.NET Core 2.2 で `MapAreaRoute` を使用するときは、[この GitHub 問題](https://github.com/aspnet/AspNetCore/issues/7772)を確認してください。</span><span class="sxs-lookup"><span data-stu-id="13552-158">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="13552-159">詳細については、[区分のルーティング](xref:mvc/controllers/routing#areas)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="13552-159">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="13552-160">MVC 区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="13552-160">Link generation with MVC areas</span></span>

<span data-ttu-id="13552-161">[サンプル ダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)に含まれる次のコードでは、区分が指定された上でリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="13552-161">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="13552-162">上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。</span><span class="sxs-lookup"><span data-stu-id="13552-162">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="13552-163">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="13552-163">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="13552-164">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="13552-164">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="13552-165">区分が指定されずに生成されたリンクは、同じ区分やコントローラーのページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13552-165">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="13552-166">区分またはコントローラーが指定されていないとき、ルーティングは*アンビエント*値に依存します。</span><span class="sxs-lookup"><span data-stu-id="13552-166">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="13552-167">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="13552-167">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="13552-168">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="13552-168">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="13552-169">詳細については、「[コントローラー アクションへのルーティング](xref:mvc/controllers/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13552-169">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="13552-170">_ViewStart.cshtml ファイルを使用した区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="13552-170">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="13552-171">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="13552-171">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="13552-172">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="13552-172">_ViewImports.cshtml</span></span>

<span data-ttu-id="13552-173">標準の場所では、 */Views/_ViewImports.cshtml* は区分に適用されません。</span><span class="sxs-lookup"><span data-stu-id="13552-173">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="13552-174">自分の領域で共通の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、`@using`、または `@inject` を使用するには、適切な *_ViewImports.cshtml* ファイルを[区分ビューに確実に適用する](xref:mvc/views/layout#importing-shared-directives)ようにします。</span><span class="sxs-lookup"><span data-stu-id="13552-174">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="13552-175">すべてのビューで同じ動作が必要な場合は、 */Views/_ViewImports.cshtml* をアプリケーション ルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="13552-175">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="13552-176">ビューが保存されている既定の区分フォルダーを変更する</span><span class="sxs-lookup"><span data-stu-id="13552-176">Change default area folder where views are stored</span></span>

<span data-ttu-id="13552-177">次のコードでは、既定の区分フォルダーが `"Areas"` から `"MyAreas"` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="13552-177">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="13552-178">Razor Pages を使った区分</span><span class="sxs-lookup"><span data-stu-id="13552-178">Areas with Razor Pages</span></span>

<span data-ttu-id="13552-179">Razor Pages を使った区分には、アプリのルートに *Areas/<area name>/Pages* フォルダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="13552-179">Areas with Razor Pages require an *Areas/<area name>/Pages* folder in the root of the app.</span></span> <span data-ttu-id="13552-180">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)では次のフォルダー構造が使われます。</span><span class="sxs-lookup"><span data-stu-id="13552-180">The following folder structure is used with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="13552-181">Project name</span><span class="sxs-lookup"><span data-stu-id="13552-181">Project name</span></span>
  * <span data-ttu-id="13552-182">Areas</span><span class="sxs-lookup"><span data-stu-id="13552-182">Areas</span></span>
    * <span data-ttu-id="13552-183">Products</span><span class="sxs-lookup"><span data-stu-id="13552-183">Products</span></span>
      * <span data-ttu-id="13552-184">Pages</span><span class="sxs-lookup"><span data-stu-id="13552-184">Pages</span></span>
        * <span data-ttu-id="13552-185">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="13552-185">_ViewImports</span></span>
        * <span data-ttu-id="13552-186">バージョン情報</span><span class="sxs-lookup"><span data-stu-id="13552-186">About</span></span>
        * <span data-ttu-id="13552-187">インデックス</span><span class="sxs-lookup"><span data-stu-id="13552-187">Index</span></span>
    * <span data-ttu-id="13552-188">サービス</span><span class="sxs-lookup"><span data-stu-id="13552-188">Services</span></span>
      * <span data-ttu-id="13552-189">Pages</span><span class="sxs-lookup"><span data-stu-id="13552-189">Pages</span></span>
        * <span data-ttu-id="13552-190">Manage</span><span class="sxs-lookup"><span data-stu-id="13552-190">Manage</span></span>
          * <span data-ttu-id="13552-191">バージョン情報</span><span class="sxs-lookup"><span data-stu-id="13552-191">About</span></span>
          * <span data-ttu-id="13552-192">インデックス</span><span class="sxs-lookup"><span data-stu-id="13552-192">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="13552-193">Razor Pages と区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="13552-193">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="13552-194">[サンプル ダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)の次のコードでは、区分を指定したリンクの生成を示しています (例: `asp-area="Products"`)。</span><span class="sxs-lookup"><span data-stu-id="13552-194">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="13552-195">上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。</span><span class="sxs-lookup"><span data-stu-id="13552-195">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="13552-196">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="13552-196">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="13552-197">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="13552-197">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="13552-198">区分が指定されずに生成されたリンクは、同じ区分のページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13552-198">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="13552-199">区分が指定されていないとき、ルーティングは "*アンビエント*" 値に依存します。</span><span class="sxs-lookup"><span data-stu-id="13552-199">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="13552-200">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="13552-200">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="13552-201">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="13552-201">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="13552-202">たとえば、次のコードから生成されるリンクを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="13552-202">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="13552-203">上のコードの場合:</span><span class="sxs-lookup"><span data-stu-id="13552-203">For the preceding code:</span></span>

* <span data-ttu-id="13552-204">`<a asp-page="/Manage/About">` から生成されるリンクは、前回の要求が `Services` 区分内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="13552-204">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="13552-205">たとえば、`/Services/Manage/`、`/Services/Manage/Index`、または `/Services/Manage/About` です。</span><span class="sxs-lookup"><span data-stu-id="13552-205">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="13552-206">`<a asp-page="/About">` から生成されるリンクは、前回の要求が `/Home` 内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="13552-206">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="13552-207">コードは、[サンプル ダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)からのものです。</span><span class="sxs-lookup"><span data-stu-id="13552-207">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="13552-208">_ViewImports ファイルを使って名前空間とタグ ヘルパーをインポートする</span><span class="sxs-lookup"><span data-stu-id="13552-208">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="13552-209">*_ViewImports.cshtml* ファイルを各区分の *Pages* フォルダーに追加して、フォルダー内の各 Razor ページに名前空間とタグ ヘルパーをインポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="13552-209">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="13552-210">サンプル コードの *Services* 区分について検討します。これには *_ViewImports.cshtml* ファイルが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="13552-210">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="13552-211">次のマークアップは */Services/Manage/About* Razor ページを表示します。</span><span class="sxs-lookup"><span data-stu-id="13552-211">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="13552-212">上のマークアップについて:</span><span class="sxs-lookup"><span data-stu-id="13552-212">In the preceding markup:</span></span>

* <span data-ttu-id="13552-213">完全修飾ドメイン名を使ってモデルを指定する必要があります (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="13552-213">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="13552-214">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` によって有効になります</span><span class="sxs-lookup"><span data-stu-id="13552-214">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="13552-215">サンプル ダウンロードでは、Products 区分に次の *_ViewImports.cshtml* ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="13552-215">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="13552-216">次のマークアップは */Products/About* Razor ページを表示します。</span><span class="sxs-lookup"><span data-stu-id="13552-216">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="13552-217">上のファイルでは、*Areas/Products/Pages/_ViewImports.cshtml* ファイルによって、名前空間と `@addTagHelper` ディレクティブがファイルにインポートされています。</span><span class="sxs-lookup"><span data-stu-id="13552-217">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="13552-218">詳細については、「[タグ ヘルパーのスコープの管理](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)」と「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13552-218">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="13552-219">Razor Pages 区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="13552-219">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="13552-220">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="13552-220">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="13552-221">区分の発行</span><span class="sxs-lookup"><span data-stu-id="13552-221">Publishing Areas</span></span>

<span data-ttu-id="13552-222">\*.csproj ファイルに `<Project Sdk="Microsoft.NET.Sdk.Web">` が含まれているときは、すべての \*.cshtml ファイル、および *wwwroot* ディレクトリ内のファイルが出力に発行されます。</span><span class="sxs-lookup"><span data-stu-id="13552-222">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
