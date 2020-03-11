---
title: ASP.NET Core の区分
author: rick-anderson
description: 区分は ASP.NET MVC の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用する方法を説明します。
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 41f7bdd6dbb3e33f843cb2a765dd30f98c81ce21
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654236"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="8a7ad-103">ASP.NET Core の区分</span><span class="sxs-lookup"><span data-stu-id="8a7ad-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="8a7ad-104">作成者: [Dhananjay Kumar](https://twitter.com/debug_mode) および [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a7ad-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a7ad-105">区分は、関連する機能を別のグループとしてグループにまとめるために使用される ASP.NET の機能です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="8a7ad-106">ルーティング用の名前空間。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-106">Namespace for routing.</span></span>
* <span data-ttu-id="8a7ad-107">ビューおよび Razor Pages のフォルダー構造。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="8a7ad-108">ルーティングを行うために、区分を使用して、別のルート パラメーター `area` を `controller` と `action` または Razor Page `page` に追加して階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="8a7ad-109">区分は、ASP.NET Core Web アプリをより小さな機能グループにパーティション分割する方法であり、分割した各グループにそれぞれの Razor Pages、コントローラー、ビュー、モデルが与えられます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="8a7ad-110">区分は、実質的にはアプリ内の構造体となります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="8a7ad-111">ASP.NET Core Web プロジェクトでは、ページ、モデル、コントローラー、ビューなどの論理コンポーネントが別々のフォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="8a7ad-112">ASP.NET Core ランタイムでは、名前付け規則を使用し、これらのコンポーネント間のリレーションシップを作成します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="8a7ad-113">大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="8a7ad-114">チェックアウト、請求、検索などの複数のビジネス ユニットがある eコマース アプリの場合です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="8a7ad-115">これらのユニットにはそれぞれ、ビュー、コントローラー、Razor Pages、モデルを含める独自の論理区分があります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="8a7ad-116">次のような場合は、プロジェクトで区分を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="8a7ad-117">論理的に大まかに区切れる複数の機能コンポーネントでアプリが構成されている。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="8a7ad-118">各機能区分を個別に使用できるようにアプリを分割したい。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="8a7ad-119">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="8a7ad-120">ダウンロード サンプルからは、区分をテストするための基本的なアプリが与えられます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="8a7ad-121">Razor Pages を使用している場合は、このドキュメントの「[Razor Pages を使った区分](#areas-with-razor-pages)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="8a7ad-122">ビューを伴うコントローラーの区分</span><span class="sxs-lookup"><span data-stu-id="8a7ad-122">Areas for controllers with views</span></span>

<span data-ttu-id="8a7ad-123">区分、コントローラー、ビューを使用する一般的な ASP.NET Core Web アプリに含まれる内容:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="8a7ad-124">[区分フォルダーの構造](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="8a7ad-125">コントローラーと区分を関連付けるための [`[Area]`](#attribute) 属性を持つコントローラー:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="8a7ad-126">[スタートアップに追加された区分ルート](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="8a7ad-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="8a7ad-127">区分フォルダーの構造</span><span class="sxs-lookup"><span data-stu-id="8a7ad-127">Area folder structure</span></span>

<span data-ttu-id="8a7ad-128">あるアプリに *Products* と *Services* という 2 つの論理グループが与えられているとします。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="8a7ad-129">区分を利用すると、フォルダーの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="8a7ad-130">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="8a7ad-130">Project name</span></span>
  * <span data-ttu-id="8a7ad-131">領域</span><span class="sxs-lookup"><span data-stu-id="8a7ad-131">Areas</span></span>
    * <span data-ttu-id="8a7ad-132">Products</span><span class="sxs-lookup"><span data-stu-id="8a7ad-132">Products</span></span>
      * <span data-ttu-id="8a7ad-133">コントローラー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-133">Controllers</span></span>
        * <span data-ttu-id="8a7ad-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8a7ad-134">HomeController.cs</span></span>
        * <span data-ttu-id="8a7ad-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="8a7ad-135">ManageController.cs</span></span>
      * <span data-ttu-id="8a7ad-136">ビュー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-136">Views</span></span>
        * <span data-ttu-id="8a7ad-137">ホーム</span><span class="sxs-lookup"><span data-stu-id="8a7ad-137">Home</span></span>
          * <span data-ttu-id="8a7ad-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-138">Index.cshtml</span></span>
        * <span data-ttu-id="8a7ad-139">[管理]</span><span class="sxs-lookup"><span data-stu-id="8a7ad-139">Manage</span></span>
          * <span data-ttu-id="8a7ad-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-140">Index.cshtml</span></span>
          * <span data-ttu-id="8a7ad-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-141">About.cshtml</span></span>
    * <span data-ttu-id="8a7ad-142">サービス</span><span class="sxs-lookup"><span data-stu-id="8a7ad-142">Services</span></span>
      * <span data-ttu-id="8a7ad-143">コントローラー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-143">Controllers</span></span>
        * <span data-ttu-id="8a7ad-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8a7ad-144">HomeController.cs</span></span>
      * <span data-ttu-id="8a7ad-145">ビュー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-145">Views</span></span>
        * <span data-ttu-id="8a7ad-146">ホーム</span><span class="sxs-lookup"><span data-stu-id="8a7ad-146">Home</span></span>
          * <span data-ttu-id="8a7ad-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-147">Index.cshtml</span></span>

<span data-ttu-id="8a7ad-148">区分を使用するとき、前述のレイアウトが一般的ですが、このフォルダー構造を使用するにはビュー ファイルのみが求められます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="8a7ad-149">ビューの検出では、一致する区分ビュー ファイルを次の順序で検索します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="8a7ad-150">コントローラーを区分に関連付ける</span><span class="sxs-lookup"><span data-stu-id="8a7ad-150">Associate the controller with an Area</span></span>

<span data-ttu-id="8a7ad-151">区分コントローラーは [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 属性で指名されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="8a7ad-152">区分ルートを追加する</span><span class="sxs-lookup"><span data-stu-id="8a7ad-152">Add Area route</span></span>

<span data-ttu-id="8a7ad-153">通常、区分ルートは、[属性ルーティング](xref:mvc/controllers/routing#ar)ではなく、通常の[ルーティング](xref:mvc/controllers/routing#cr)を使用します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="8a7ad-154">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="8a7ad-155">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="8a7ad-156">URL スペースがすべての区分で統一されている場合、ルート テンプレートでトークンとして `{area:...}` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="8a7ad-157">上記のコードでは、ルートは 1 つの区分に一致しなければならないという制約が `exists` によって適用されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="8a7ad-158">`MapControllerRoute`での `{area:...}` の使用:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="8a7ad-159">は、エリアにルーティングを追加するための最も複雑なメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="8a7ad-160">すべてのコントローラーを `[Area("Area name")]` 属性と照合します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="8a7ad-161">次のコードでは、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> を使用し、名前の付いた区分ルートが 2 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="8a7ad-162">詳細については、[区分のルーティング](xref:mvc/controllers/routing#areas)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="8a7ad-163">MVC 区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="8a7ad-163">Link generation with MVC areas</span></span>

<span data-ttu-id="8a7ad-164">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)に含まれる次のコードでは、区分が指定された上でリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="8a7ad-165">サンプルダウンロードには、次のものを含む[部分ビュー](xref:mvc/views/partial)が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="8a7ad-166">前のリンク。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-166">The preceding links.</span></span>
* <span data-ttu-id="8a7ad-167">前の例と同様のリンク `area` が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="8a7ad-168">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="8a7ad-169">区分が指定されずに生成されたリンクは、同じ区分やコントローラーのページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="8a7ad-170">区分またはコントローラーが指定されていないとき、ルーティングは[アンビエント](xref:mvc/controllers/routing#ambient)値に依存します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="8a7ad-171">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="8a7ad-172">サンプルアプリの多くの場合、アンビエント値を使用すると、領域が指定されていないマークアップとの正しくないリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="8a7ad-173">詳細については、「[コントローラー アクションへのルーティング](xref:mvc/controllers/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="8a7ad-174">_ViewStart.cshtml ファイルを使用した区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="8a7ad-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="8a7ad-175">アプリ全体の共通レイアウトを共有するには、[アプリケーションのルートフォルダー](#arf)にある *_ViewStart*を保持します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="8a7ad-176">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="8a7ad-177">アプリケーションルートフォルダー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-177">Application root folder</span></span>

<span data-ttu-id="8a7ad-178">アプリケーションルートフォルダーは、ASP.NET Core テンプレートを使用して作成された web アプリ内の*Startup.cs*を含むフォルダーです。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="8a7ad-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="8a7ad-180">/ビュー */_ViewImports*、MVC の場合は、Razor Pages の場合は *_ViewImports*で、領域内のビューにはインポートされません。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="8a7ad-181">次のいずれかの方法を使用して、ビューをすべてのビューにインポートします。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="8a7ad-182">[アプリケーションのルートフォルダー](#arf)に _ViewImports を追加し*ます*。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="8a7ad-183">アプリケーションルートフォルダー内の *_ViewImports*は、アプリ内のすべてのビューに適用されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="8a7ad-184">[区分] の下にある適切な表示フォルダーに _ViewImports ファイルをコピーし*ます*。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="8a7ad-185">*_ViewImports*のファイルには、通常、[タグヘルパー](xref:mvc/views/tag-helpers/intro)の imports、`@using`、および `@inject` ステートメントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="8a7ad-186">詳細については、「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="8a7ad-187">ビューが保存されている既定の区分フォルダーを変更する</span><span class="sxs-lookup"><span data-stu-id="8a7ad-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="8a7ad-188">次のコードでは、既定の区分フォルダーが `"Areas"` から `"MyAreas"` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="8a7ad-189">Razor Pages を使った区分</span><span class="sxs-lookup"><span data-stu-id="8a7ad-189">Areas with Razor Pages</span></span>

<span data-ttu-id="8a7ad-190">Razor Pages の領域には、アプリのルートに `Areas/<area name>/Pages` フォルダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="8a7ad-191">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)では次のフォルダー構造が使われます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="8a7ad-192">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="8a7ad-192">Project name</span></span>
  * <span data-ttu-id="8a7ad-193">領域</span><span class="sxs-lookup"><span data-stu-id="8a7ad-193">Areas</span></span>
    * <span data-ttu-id="8a7ad-194">Products</span><span class="sxs-lookup"><span data-stu-id="8a7ad-194">Products</span></span>
      * <span data-ttu-id="8a7ad-195">ページ</span><span class="sxs-lookup"><span data-stu-id="8a7ad-195">Pages</span></span>
        * <span data-ttu-id="8a7ad-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="8a7ad-196">_ViewImports</span></span>
        * <span data-ttu-id="8a7ad-197">概要</span><span class="sxs-lookup"><span data-stu-id="8a7ad-197">About</span></span>
        * <span data-ttu-id="8a7ad-198">Index</span><span class="sxs-lookup"><span data-stu-id="8a7ad-198">Index</span></span>
    * <span data-ttu-id="8a7ad-199">サービス</span><span class="sxs-lookup"><span data-stu-id="8a7ad-199">Services</span></span>
      * <span data-ttu-id="8a7ad-200">ページ</span><span class="sxs-lookup"><span data-stu-id="8a7ad-200">Pages</span></span>
        * <span data-ttu-id="8a7ad-201">[管理]</span><span class="sxs-lookup"><span data-stu-id="8a7ad-201">Manage</span></span>
          * <span data-ttu-id="8a7ad-202">概要</span><span class="sxs-lookup"><span data-stu-id="8a7ad-202">About</span></span>
          * <span data-ttu-id="8a7ad-203">Index</span><span class="sxs-lookup"><span data-stu-id="8a7ad-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="8a7ad-204">Razor Pages と区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="8a7ad-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="8a7ad-205">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)の次のコードでは、区分を指定したリンクの生成を示しています (例: `asp-area="Products"`)。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="8a7ad-206">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="8a7ad-207">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="8a7ad-208">区分が指定されずに生成されたリンクは、同じ区分のページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="8a7ad-209">区分が指定されていないとき、ルーティングは "*アンビエント*" 値に依存します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="8a7ad-210">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="8a7ad-211">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="8a7ad-212">たとえば、次のコードから生成されるリンクを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="8a7ad-213">上のコードの場合:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-213">For the preceding code:</span></span>

* <span data-ttu-id="8a7ad-214">`<a asp-page="/Manage/About">` から生成されるリンクは、前回の要求が `Services` 区分内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="8a7ad-215">たとえば、「`/Services/Manage/`」、「`/Services/Manage/Index`」、「`/Services/Manage/About`」のように指定します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="8a7ad-216">`<a asp-page="/About">` から生成されるリンクは、前回の要求が `/Home` 内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="8a7ad-217">コードは、[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas)からのものです。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="8a7ad-218">_ViewImports ファイルを使って名前空間とタグ ヘルパーをインポートする</span><span class="sxs-lookup"><span data-stu-id="8a7ad-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="8a7ad-219">*_ViewImports.cshtml* ファイルを各区分の *Pages* フォルダーに追加して、フォルダー内の各 Razor ページに名前空間とタグ ヘルパーをインポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="8a7ad-220">サンプル コードの *Services* 区分について検討します。これには *_ViewImports.cshtml* ファイルが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="8a7ad-221">次のマークアップは */Services/Manage/About* Razor ページを表示します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="8a7ad-222">上のマークアップについて:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-222">In the preceding markup:</span></span>

* <span data-ttu-id="8a7ad-223">完全修飾ドメイン名を使ってモデルを指定する必要があります (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="8a7ad-224">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` によって有効になります</span><span class="sxs-lookup"><span data-stu-id="8a7ad-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="8a7ad-225">サンプル ダウンロードでは、Products 区分に次の *_ViewImports.cshtml* ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="8a7ad-226">次のマークアップは */Products/About* Razor ページを表示します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="8a7ad-227">上のファイルでは、`@addTagHelper`Areas/Products/Pages/_ViewImports.cshtml*ファイルによって、名前空間と* ディレクティブがファイルにインポートされています。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="8a7ad-228">詳細については、「[タグ ヘルパーのスコープの管理](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)」と「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="8a7ad-229">Razor Pages 区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="8a7ad-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="8a7ad-230">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="8a7ad-231">区分の発行</span><span class="sxs-lookup"><span data-stu-id="8a7ad-231">Publishing Areas</span></span>

<span data-ttu-id="8a7ad-232">\*.csproj ファイルに \*が含まれているときは、すべての *.cshtml ファイル、および*wwwroot`<Project Sdk="Microsoft.NET.Sdk.Web">` ディレクトリ内のファイルが出力に発行されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a7ad-233">区分は ASP.NET の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="8a7ad-234">ルーティングを行うために、区分を使用して、別のルート パラメーター `area` を `controller` と `action` または Razor Page `page` に追加して階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="8a7ad-235">区分は、ASP.NET Core Web アプリをより小さな機能グループにパーティション分割する方法であり、分割した各グループにそれぞれの Razor Pages、コントローラー、ビュー、モデルが与えられます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="8a7ad-236">区分は、実質的にはアプリ内の構造体となります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="8a7ad-237">ASP.NET Core Web プロジェクトでは、ページ、モデル、コントローラー、ビューなどの論理コンポーネントが別々のフォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="8a7ad-238">ASP.NET Core ランタイムでは、名前付け規則を使用し、これらのコンポーネント間のリレーションシップを作成します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="8a7ad-239">大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="8a7ad-240">チェックアウト、請求、検索などの複数のビジネス ユニットがある eコマース アプリの場合です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="8a7ad-241">これらのユニットにはそれぞれ、ビュー、コントローラー、Razor Pages、モデルを含める独自の論理区分があります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="8a7ad-242">次のような場合は、プロジェクトで区分を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="8a7ad-243">論理的に大まかに区切れる複数の機能コンポーネントでアプリが構成されている。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="8a7ad-244">各機能区分を個別に使用できるようにアプリを分割したい。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="8a7ad-245">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="8a7ad-246">ダウンロード サンプルからは、区分をテストするための基本的なアプリが与えられます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="8a7ad-247">Razor Pages を使用している場合は、このドキュメントの「[Razor Pages を使った区分](#areas-with-razor-pages)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="8a7ad-248">ビューを伴うコントローラーの区分</span><span class="sxs-lookup"><span data-stu-id="8a7ad-248">Areas for controllers with views</span></span>

<span data-ttu-id="8a7ad-249">区分、コントローラー、ビューを使用する一般的な ASP.NET Core Web アプリに含まれる内容:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="8a7ad-250">[区分フォルダーの構造](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="8a7ad-251">コントローラーと区分を関連付けるための [`[Area]`](#attribute) 属性を持つコントローラー:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="8a7ad-252">[スタートアップに追加された区分ルート](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="8a7ad-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="8a7ad-253">区分フォルダーの構造</span><span class="sxs-lookup"><span data-stu-id="8a7ad-253">Area folder structure</span></span>

<span data-ttu-id="8a7ad-254">あるアプリに *Products* と *Services* という 2 つの論理グループが与えられているとします。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="8a7ad-255">区分を利用すると、フォルダーの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="8a7ad-256">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="8a7ad-256">Project name</span></span>
  * <span data-ttu-id="8a7ad-257">領域</span><span class="sxs-lookup"><span data-stu-id="8a7ad-257">Areas</span></span>
    * <span data-ttu-id="8a7ad-258">Products</span><span class="sxs-lookup"><span data-stu-id="8a7ad-258">Products</span></span>
      * <span data-ttu-id="8a7ad-259">コントローラー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-259">Controllers</span></span>
        * <span data-ttu-id="8a7ad-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8a7ad-260">HomeController.cs</span></span>
        * <span data-ttu-id="8a7ad-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="8a7ad-261">ManageController.cs</span></span>
      * <span data-ttu-id="8a7ad-262">ビュー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-262">Views</span></span>
        * <span data-ttu-id="8a7ad-263">ホーム</span><span class="sxs-lookup"><span data-stu-id="8a7ad-263">Home</span></span>
          * <span data-ttu-id="8a7ad-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-264">Index.cshtml</span></span>
        * <span data-ttu-id="8a7ad-265">[管理]</span><span class="sxs-lookup"><span data-stu-id="8a7ad-265">Manage</span></span>
          * <span data-ttu-id="8a7ad-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-266">Index.cshtml</span></span>
          * <span data-ttu-id="8a7ad-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-267">About.cshtml</span></span>
    * <span data-ttu-id="8a7ad-268">サービス</span><span class="sxs-lookup"><span data-stu-id="8a7ad-268">Services</span></span>
      * <span data-ttu-id="8a7ad-269">コントローラー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-269">Controllers</span></span>
        * <span data-ttu-id="8a7ad-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8a7ad-270">HomeController.cs</span></span>
      * <span data-ttu-id="8a7ad-271">ビュー</span><span class="sxs-lookup"><span data-stu-id="8a7ad-271">Views</span></span>
        * <span data-ttu-id="8a7ad-272">ホーム</span><span class="sxs-lookup"><span data-stu-id="8a7ad-272">Home</span></span>
          * <span data-ttu-id="8a7ad-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-273">Index.cshtml</span></span>

<span data-ttu-id="8a7ad-274">区分を使用するとき、前述のレイアウトが一般的ですが、このフォルダー構造を使用するにはビュー ファイルのみが求められます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="8a7ad-275">ビューの検出では、一致する区分ビュー ファイルを次の順序で検索します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="8a7ad-276">コントローラーを区分に関連付ける</span><span class="sxs-lookup"><span data-stu-id="8a7ad-276">Associate the controller with an Area</span></span>

<span data-ttu-id="8a7ad-277">区分コントローラーは [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 属性で指名されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="8a7ad-278">区分ルートを追加する</span><span class="sxs-lookup"><span data-stu-id="8a7ad-278">Add Area route</span></span>

<span data-ttu-id="8a7ad-279">区分ルートでは通常、属性ルーティングではなく、従来のルーティングが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="8a7ad-280">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="8a7ad-281">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="8a7ad-282">URL スペースがすべての区分で統一されている場合、ルート テンプレートでトークンとして `{area:...}` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="8a7ad-283">上記のコードでは、ルートは 1 つの区分に一致しなければならないという制約が `exists` によって適用されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="8a7ad-284">`{area:...}` の使用は、ルーティングを区分に追加するメカニズムとして最も単純です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="8a7ad-285">次のコードでは、<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> を使用し、名前の付いた区分ルートが 2 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="8a7ad-286">ASP.NET Core 2.2 で `MapAreaRoute` を使用するときは、[この GitHub 問題](https://github.com/dotnet/AspNetCore/issues/7772)を確認してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="8a7ad-287">詳細については、[区分のルーティング](xref:mvc/controllers/routing#areas)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="8a7ad-288">MVC 区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="8a7ad-288">Link generation with MVC areas</span></span>

<span data-ttu-id="8a7ad-289">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)に含まれる次のコードでは、区分が指定された上でリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="8a7ad-290">上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="8a7ad-291">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="8a7ad-292">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="8a7ad-293">区分が指定されずに生成されたリンクは、同じ区分やコントローラーのページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="8a7ad-294">区分またはコントローラーが指定されていないとき、ルーティングは*アンビエント*値に依存します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="8a7ad-295">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="8a7ad-296">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="8a7ad-297">詳細については、「[コントローラー アクションへのルーティング](xref:mvc/controllers/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="8a7ad-298">_ViewStart.cshtml ファイルを使用した区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="8a7ad-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="8a7ad-299">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="8a7ad-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="8a7ad-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="8a7ad-301">標準の場所では、 */Views/_ViewImports.cshtml* は区分に適用されません。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="8a7ad-302">自分の領域で共通の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、`@using`、または `@inject` を使用するには、適切な *_ViewImports.cshtml* ファイルを[区分ビューに確実に適用する](xref:mvc/views/layout#importing-shared-directives)ようにします。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="8a7ad-303">すべてのビューで同じ動作が必要な場合は、 */Views/_ViewImports.cshtml* をアプリケーション ルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="8a7ad-304">ビューが保存されている既定の区分フォルダーを変更する</span><span class="sxs-lookup"><span data-stu-id="8a7ad-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="8a7ad-305">次のコードでは、既定の区分フォルダーが `"Areas"` から `"MyAreas"` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="8a7ad-306">Razor Pages を使った区分</span><span class="sxs-lookup"><span data-stu-id="8a7ad-306">Areas with Razor Pages</span></span>

<span data-ttu-id="8a7ad-307">Razor Pages の領域には、アプリのルートに `Areas/<area name>/Pages` フォルダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="8a7ad-308">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)では次のフォルダー構造が使われます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="8a7ad-309">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="8a7ad-309">Project name</span></span>
  * <span data-ttu-id="8a7ad-310">領域</span><span class="sxs-lookup"><span data-stu-id="8a7ad-310">Areas</span></span>
    * <span data-ttu-id="8a7ad-311">Products</span><span class="sxs-lookup"><span data-stu-id="8a7ad-311">Products</span></span>
      * <span data-ttu-id="8a7ad-312">ページ</span><span class="sxs-lookup"><span data-stu-id="8a7ad-312">Pages</span></span>
        * <span data-ttu-id="8a7ad-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="8a7ad-313">_ViewImports</span></span>
        * <span data-ttu-id="8a7ad-314">概要</span><span class="sxs-lookup"><span data-stu-id="8a7ad-314">About</span></span>
        * <span data-ttu-id="8a7ad-315">Index</span><span class="sxs-lookup"><span data-stu-id="8a7ad-315">Index</span></span>
    * <span data-ttu-id="8a7ad-316">サービス</span><span class="sxs-lookup"><span data-stu-id="8a7ad-316">Services</span></span>
      * <span data-ttu-id="8a7ad-317">ページ</span><span class="sxs-lookup"><span data-stu-id="8a7ad-317">Pages</span></span>
        * <span data-ttu-id="8a7ad-318">[管理]</span><span class="sxs-lookup"><span data-stu-id="8a7ad-318">Manage</span></span>
          * <span data-ttu-id="8a7ad-319">概要</span><span class="sxs-lookup"><span data-stu-id="8a7ad-319">About</span></span>
          * <span data-ttu-id="8a7ad-320">Index</span><span class="sxs-lookup"><span data-stu-id="8a7ad-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="8a7ad-321">Razor Pages と区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="8a7ad-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="8a7ad-322">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)の次のコードでは、区分を指定したリンクの生成を示しています (例: `asp-area="Products"`)。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="8a7ad-323">上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="8a7ad-324">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="8a7ad-325">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="8a7ad-326">区分が指定されずに生成されたリンクは、同じ区分のページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="8a7ad-327">区分が指定されていないとき、ルーティングは "*アンビエント*" 値に依存します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="8a7ad-328">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="8a7ad-329">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="8a7ad-330">たとえば、次のコードから生成されるリンクを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="8a7ad-331">上のコードの場合:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-331">For the preceding code:</span></span>

* <span data-ttu-id="8a7ad-332">`<a asp-page="/Manage/About">` から生成されるリンクは、前回の要求が `Services` 区分内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="8a7ad-333">たとえば、「`/Services/Manage/`」、「`/Services/Manage/Index`」、「`/Services/Manage/About`」のように指定します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="8a7ad-334">`<a asp-page="/About">` から生成されるリンクは、前回の要求が `/Home` 内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="8a7ad-335">コードは、[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)からのものです。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="8a7ad-336">_ViewImports ファイルを使って名前空間とタグ ヘルパーをインポートする</span><span class="sxs-lookup"><span data-stu-id="8a7ad-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="8a7ad-337">*_ViewImports.cshtml* ファイルを各区分の *Pages* フォルダーに追加して、フォルダー内の各 Razor ページに名前空間とタグ ヘルパーをインポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="8a7ad-338">サンプル コードの *Services* 区分について検討します。これには *_ViewImports.cshtml* ファイルが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="8a7ad-339">次のマークアップは */Services/Manage/About* Razor ページを表示します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="8a7ad-340">上のマークアップについて:</span><span class="sxs-lookup"><span data-stu-id="8a7ad-340">In the preceding markup:</span></span>

* <span data-ttu-id="8a7ad-341">完全修飾ドメイン名を使ってモデルを指定する必要があります (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="8a7ad-342">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` によって有効になります</span><span class="sxs-lookup"><span data-stu-id="8a7ad-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="8a7ad-343">サンプル ダウンロードでは、Products 区分に次の *_ViewImports.cshtml* ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="8a7ad-344">次のマークアップは */Products/About* Razor ページを表示します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="8a7ad-345">上のファイルでは、`@addTagHelper`Areas/Products/Pages/_ViewImports.cshtml*ファイルによって、名前空間と* ディレクティブがファイルにインポートされています。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="8a7ad-346">詳細については、「[タグ ヘルパーのスコープの管理](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)」と「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="8a7ad-347">Razor Pages 区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="8a7ad-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="8a7ad-348">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="8a7ad-349">区分の発行</span><span class="sxs-lookup"><span data-stu-id="8a7ad-349">Publishing Areas</span></span>

<span data-ttu-id="8a7ad-350">\*.csproj ファイルに \*が含まれているときは、すべての *.cshtml ファイル、および*wwwroot`<Project Sdk="Microsoft.NET.Sdk.Web">` ディレクトリ内のファイルが出力に発行されます。</span><span class="sxs-lookup"><span data-stu-id="8a7ad-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
