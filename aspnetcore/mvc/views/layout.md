---
title: ASP.NET Core でのレイアウト
author: ardalis
description: 共通レイアウトの使用方法、ディレクティブの共有方法、および ASP.NET Core アプリでビューをレンダリングする前に共通コードを実行する方法について説明します。
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: 9a7b8003b24329f6e9cbd349ee47f6844b7c5f6d
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238032"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="9274b-103">ASP.NET Core でのレイアウト</span><span class="sxs-lookup"><span data-stu-id="9274b-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="9274b-104">作成者: [Steve Smith](https://ardalis.com/)、[Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="9274b-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="9274b-105">ページやビューは、多くの場合、ビジュアルおよびプログラムの要素を共有します。</span><span class="sxs-lookup"><span data-stu-id="9274b-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="9274b-106">この記事では、次の方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9274b-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="9274b-107">共通のレイアウトを使用する。</span><span class="sxs-lookup"><span data-stu-id="9274b-107">Use common layouts.</span></span>
* <span data-ttu-id="9274b-108">ディレクティブを共有する。</span><span class="sxs-lookup"><span data-stu-id="9274b-108">Share directives.</span></span>
* <span data-ttu-id="9274b-109">ページまたはビューを表示する前に、共通のコードを実行する。</span><span class="sxs-lookup"><span data-stu-id="9274b-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="9274b-110">このドキュメントでは、ASP.NET Core MVC に対する 2 つの異なるアプローチに向けたレイアウトについて説明します:Razor Pages と、ビューを含むコントローラーです。</span><span class="sxs-lookup"><span data-stu-id="9274b-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="9274b-111">このトピックでは、違いは最小限です。</span><span class="sxs-lookup"><span data-stu-id="9274b-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="9274b-112">Razor Pages は、*Pages* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="9274b-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="9274b-113">ビューを含むコントローラーでは、*Views* フォルダーをビューに使用します。</span><span class="sxs-lookup"><span data-stu-id="9274b-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="9274b-114">レイアウトとは</span><span class="sxs-lookup"><span data-stu-id="9274b-114">What is a Layout</span></span>

<span data-ttu-id="9274b-115">ほとんどの Web アプリには、ユーザーがページ間を移動する際に一貫性のあるエクスペリエンスを提供する共通レイアウトがあります。</span><span class="sxs-lookup"><span data-stu-id="9274b-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="9274b-116">通常、このレイアウトには、アプリのヘッダー、ナビゲーションまたはメニュー要素、フッターなどの共通のユーザー インターフェイス要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9274b-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![ページ レイアウトの例](layout/_static/page-layout.png)

<span data-ttu-id="9274b-118">スクリプトやスタイルシートなどの共通の HTML 構造体も、アプリ内の多くのページでよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="9274b-119">これらの共有要素をすべて *layout* ファイルで定義することで、アプリ内で使用する任意のビューで参照できるようになります。</span><span class="sxs-lookup"><span data-stu-id="9274b-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="9274b-120">レイアウトにより、ビュー内の重複コードが減ります。</span><span class="sxs-lookup"><span data-stu-id="9274b-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="9274b-121">規則により、ASP.NET Core アプリの既定のレイアウトには *_Layout.cshtml* という名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="9274b-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="9274b-122">テンプレートを使用すると、次のような新しい ASP.NET Core プロジェクトのレイアウト ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="9274b-123">Razor Pages:*Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9274b-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![ソリューション エクスプローラーの Pages フォルダー](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="9274b-125">ビューを含むコントローラー:*Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9274b-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![ソリューション エクスプローラーの Views フォルダー](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="9274b-127">レイアウトでは、アプリのビューの最上位のテンプレートが定義されています。</span><span class="sxs-lookup"><span data-stu-id="9274b-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="9274b-128">アプリでは、レイアウトは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9274b-128">Apps don't require a layout.</span></span> <span data-ttu-id="9274b-129">アプリでは、それぞれ異なるレイアウトを指定するさまざまなビューを使用して、複数のレイアウトを定義できます。</span><span class="sxs-lookup"><span data-stu-id="9274b-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="9274b-130">次のコードでは、コントローラーとビューを含むテンプレートで作成されたプロジェクトのレイアウト ファイルを示します。</span><span class="sxs-lookup"><span data-stu-id="9274b-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="9274b-131">レイアウトの指定</span><span class="sxs-lookup"><span data-stu-id="9274b-131">Specifying a Layout</span></span>

<span data-ttu-id="9274b-132">Razor ビューには `Layout` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="9274b-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="9274b-133">個々のビューは、このプロパティを設定することでレイアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="9274b-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="9274b-134">指定されるレイアウトでは、完全なパス (例: */Pages/Shared/_Layout.cshtml*、 */Views/Shared/_Layout.cshtml*) または部分パス (例: `_Layout`) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9274b-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="9274b-135">部分的な名前を指定すると、Razor ビュー エンジンが標準の検出プロセスを使用して、レイアウト ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="9274b-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="9274b-136">ハンドラー メソッド (またはコントローラー) が存在するフォルダーが最初に検索され、その後で *Shared* フォルダーが検索されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="9274b-137">この検出プロセスは、[部分ビュー](xref:mvc/views/partial#partial-view-discovery)の検出に使用されるのと同じプロセスです。</span><span class="sxs-lookup"><span data-stu-id="9274b-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="9274b-138">既定では、すべてのレイアウトで `RenderBody` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="9274b-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="9274b-139">`RenderBody` への呼び出しが配置されると、ビューのコンテンツがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="9274b-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="9274b-140">セクション</span><span class="sxs-lookup"><span data-stu-id="9274b-140">Sections</span></span>

<span data-ttu-id="9274b-141">レイアウトは、必要に応じて `RenderSection` を呼び出すことで、1 つ以上の*セクション*を参照することができます。</span><span class="sxs-lookup"><span data-stu-id="9274b-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="9274b-142">セクションは、特定のページ要素の配置場所を整理する方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="9274b-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="9274b-143">`RenderSection` の呼び出しごとに、そのセクションを必須またはオプションにするかどうかを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9274b-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

<span data-ttu-id="9274b-144">必須のセクションが見つからない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="9274b-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="9274b-145">個々のビューは、`@section` Razor 構文を使用して、セクション内にレンダリングされるコンテンツを指定します。</span><span class="sxs-lookup"><span data-stu-id="9274b-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="9274b-146">ページまたはビューでセクションを定義する場合は、レンダリングされる必要があります (そうしないと、エラーが発生します)。</span><span class="sxs-lookup"><span data-stu-id="9274b-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="9274b-147">Razor Pages ビューでの `@section` 定義の例:</span><span class="sxs-lookup"><span data-stu-id="9274b-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

<span data-ttu-id="9274b-148">上記のコードでは、*scripts/main.js* がページまたはビューの `scripts` セクションに追加されています。</span><span class="sxs-lookup"><span data-stu-id="9274b-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="9274b-149">同じアプリの他のページまたはビューではこのスクリプトは必要なく、スクリプト セクションは定義されていません。</span><span class="sxs-lookup"><span data-stu-id="9274b-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="9274b-150">次のマークアップでは、[部分タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)を使用して *_ValidationScriptsPartial.cshtml* を表示しています。</span><span class="sxs-lookup"><span data-stu-id="9274b-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="9274b-151">上記のマークアップは、[スキャフォールディング ID](xref:security/authentication/scaffold-identity) によって生成されました。</span><span class="sxs-lookup"><span data-stu-id="9274b-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="9274b-152">ページまたはビューで定義されたセクションは、そのイミディエイト レイアウト ページでのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="9274b-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="9274b-153">これらは、部分、ビュー コンポーネント、またはビュー システムの他の部分からは参照できません。</span><span class="sxs-lookup"><span data-stu-id="9274b-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="9274b-154">セクションの無視</span><span class="sxs-lookup"><span data-stu-id="9274b-154">Ignoring sections</span></span>

<span data-ttu-id="9274b-155">既定では、コンテンツ ページの本文とすべてのセクションがレイアウト ページですべてレンダリングされる必要があります。</span><span class="sxs-lookup"><span data-stu-id="9274b-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="9274b-156">Razor ビュー エンジンは、本文と各セクションがレンダリングされているかどうかを追跡することによってこれを実行します。</span><span class="sxs-lookup"><span data-stu-id="9274b-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="9274b-157">本文またはセクションを無視するようにビュー エンジンに指示するには、`IgnoreBody` メソッドと `IgnoreSection` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9274b-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="9274b-158">Razor ページ内の本文とすべてのセクションは、レンダリングされるか無視される必要があります。</span><span class="sxs-lookup"><span data-stu-id="9274b-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="9274b-159">共有ディレクティブのインポート</span><span class="sxs-lookup"><span data-stu-id="9274b-159">Importing Shared Directives</span></span>

<span data-ttu-id="9274b-160">ビューやページでは、Razor ディレクティブを使用して名前空間をインポートし、[依存関係の挿入](dependency-injection.md)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9274b-160">Views and pages can use Razor directives to importing namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="9274b-161">多くのビューで共有されるディレクティブは、共通の *_ViewImports.cshtml* ファイルで指定できます。</span><span class="sxs-lookup"><span data-stu-id="9274b-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9274b-162">`_ViewImports` ファイルは、次のディレクティブをサポートします。</span><span class="sxs-lookup"><span data-stu-id="9274b-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="9274b-163">このファイルは、関数やセクションの定義などの Razor 機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="9274b-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="9274b-164">`_ViewImports.cshtml` ファイルのサンプル:</span><span class="sxs-lookup"><span data-stu-id="9274b-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="9274b-165">ASP.NET Core MVC アプリの *_ViewImports.cshtml* ファイルは、通常、*Pages* (または *Views*) フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="9274b-166">*_ViewImports.cshtml* ファイルは、任意のフォルダー内に配置できますが、その場合は、そのフォルダーとそのサブフォルダー内にあるパージまたはビューにのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="9274b-167">`_ViewImports` ファイルの処理はルート レベルで開始されてから、フォルダーごとに、ページまたはビュー自体の場所に至るまで行われます。</span><span class="sxs-lookup"><span data-stu-id="9274b-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="9274b-168">ルート レベルで指定された `_ViewImports` の設定は、フォルダー レベルでオーバーライドされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9274b-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="9274b-169">たとえば、次のように想定します。</span><span class="sxs-lookup"><span data-stu-id="9274b-169">For example, suppose:</span></span>

* <span data-ttu-id="9274b-170">ルート レベルの *_ViewImports.cshtml* ファイルには、`@model MyModel1` と `@addTagHelper *, MyTagHelper1` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9274b-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="9274b-171">サブフォルダーの *_ViewImports.cshtml* ファイルには、`@model MyModel2` と `@addTagHelper *, MyTagHelper2` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9274b-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="9274b-172">サブフォルダー内のページおよびビューは、タグ ヘルパーと `MyModel2` モデルの両方にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9274b-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="9274b-173">複数の *_ViewImports.cshtml* ファイルがファイル階層内にある場合、ディレクティブの結合された動作は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9274b-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="9274b-174">`@addTagHelper`、`@removeTagHelper`: 順番どおりにすべて実行</span><span class="sxs-lookup"><span data-stu-id="9274b-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="9274b-175">`@tagHelperPrefix`: ビューに最も近いものが、他のものをすべてをオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="9274b-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="9274b-176">`@model`: ビューに最も近いものが、他のものをすべてをオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="9274b-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="9274b-177">`@inherits`: ビューに最も近いものが、他のものをすべてをオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="9274b-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="9274b-178">`@using`: すべてが含まれ、重複は無視される</span><span class="sxs-lookup"><span data-stu-id="9274b-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="9274b-179">`@inject`: プロパティごとに、ビューに最も近いものが、同じプロパティ名を持つ他のものすべてをオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="9274b-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="9274b-180">各ビューの前にコードを実行する</span><span class="sxs-lookup"><span data-stu-id="9274b-180">Running Code Before Each View</span></span>

<span data-ttu-id="9274b-181">各ビューまたはページの前に実行する必要があるコードは、 *_ViewStart.cshtml* ファイルに配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9274b-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="9274b-182">慣例により、 *_ViewStart.cshtml* ファイルは *Pages* (または *Views*) フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="9274b-183">*_ViewStart.cshtml* に列記されているステートメントは、すべての (レイアウトでもなく、部分ビューでもない) 完全なビューより前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="9274b-184">[ViewImports.cshtml](xref:mvc/views/layout#viewimports) と同様に、 *_ViewStart.cshtml* は階層構造です。</span><span class="sxs-lookup"><span data-stu-id="9274b-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="9274b-185">*_ViewStart.cshtml* ファイルがビューまたはページ フォルダーで定義されている場合、*Pages* (または *Views*) フォルダーのルートで定義されているファイル (ある場合) の後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9274b-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="9274b-186">*_ViewStart.cshtml* ファイルのサンプル:</span><span class="sxs-lookup"><span data-stu-id="9274b-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="9274b-187">上記のファイルは、すべてのビューで *_Layout.cshtml* レイアウトを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="9274b-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="9274b-188">通常、 *_ViewStart.cshtml* および *_ViewImports.cshtml* は、 */Pages/Shared* (または */Views/Shared*) フォルダーには配置**されません**。</span><span class="sxs-lookup"><span data-stu-id="9274b-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="9274b-189">これらのファイルのアプリ レベルのバージョンは、 */Pages* (または */Views*) フォルダーに直接配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9274b-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
