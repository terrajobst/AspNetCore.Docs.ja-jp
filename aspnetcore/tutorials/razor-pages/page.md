---
title: ASP.NET Core でスキャフォールディングされた Razor ページ
author: rick-anderson
description: スキャフォールディングによって生成された Razor ページについて説明します。
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: 594fd6186cc73aa054fc9a1478850fa01e481ef2
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034193"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="a0fbc-103">ASP.NET Core でスキャフォールディングされた Razor ページ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0fbc-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a0fbc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a0fbc-105">このチュートリアルでは、[前のチュートリアル](xref:tutorials/razor-pages/model)でスキャフォールディングによって作成された Razor ページについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="a0fbc-106">[作成]、[削除]、[詳細]、および [編集] ページ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="a0fbc-107">次のように、*Pages/Movies/Index.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="a0fbc-108">Razor ページは `PageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="a0fbc-109">慣例により、`PageModel` 派生クラスは `<PageName>Model` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="a0fbc-110">コンストラクターは[依存性の注入](xref:fundamentals/dependency-injection)を使用して、`RazorPagesMovieContext` をページに追加します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="a0fbc-111">スキャフォールディングされたページではすべてこのパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="a0fbc-112">Entity Framework での非同期プログラミングの詳細については、「[非同期コード](xref:data/ef-rp/intro#asynchronous-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="a0fbc-113">ページに対して要求が行われると、`OnGetAsync` メソッドは Razor ページにムービーのリストを返します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="a0fbc-114">`OnGetAsync` または `OnGet` が呼び出され、ページの状態が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="a0fbc-115">この場合、`OnGetAsync` でムービーのリストを取得し、表示します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="a0fbc-116">`OnGet` によって `void` が返される場合、または `OnGetAsync` によって `Task` が返される場合は、return ステートメントは使用されません。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="a0fbc-117">戻り値の型が `IActionResult` または `Task<IActionResult>` の場合は、return ステートメントを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="a0fbc-118">たとえば、*Pages/Movies/Create.cshtml.cs* `OnPostAsync` メソッドでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="a0fbc-119">次のように、*Pages/Movies/Index.cshtml* Razor ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="a0fbc-120">Razor では、HTML から C# または Razor 固有のマークアップに移行できます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="a0fbc-121">`@` シンボルの後に [Razor で予約済みのキーワード](xref:mvc/views/razor#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。それ以外の場合は、C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="a0fbc-122">@page ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-122">The @page directive</span></span>

<span data-ttu-id="a0fbc-123">`@page` Razor ディレクティブを使うと、ファイルが MVC アクションになります。つまり、これで要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="a0fbc-124">`@page` はページで最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a0fbc-125">`@page` は、Razor 固有のマークアップへの移行の例です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="a0fbc-126">詳細については、「[Razor syntax](xref:mvc/views/razor#razor-syntax)」 (Razor の構文) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="a0fbc-127">次の HTML ヘルパーで使用されるラムダ式を確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="a0fbc-128">`DisplayNameFor` HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="a0fbc-129">ラムダ式は評価されるのではなく検査されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="a0fbc-130">これは、`model`、`model.Movie`、または `model.Movie[0]` が `null` または空である場合、アクセス違反がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="a0fbc-131">ラムダ式が (`@Html.DisplayFor(modelItem => item.Title)` などを使用して) 評価される場合は、モデルのプロパティ値が評価されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="a0fbc-132">@model ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="a0fbc-133">`@model` ディレクティブは、Razor ページに渡されるモデルの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="a0fbc-134">前の例の `@model` 行は、Razor ページで `PageModel` 派生クラスを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="a0fbc-135">モデルは、ページの `@Html.DisplayNameFor` および `@Html.DisplayFor` [HTML ヘルパーで](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="a0fbc-136">レイアウト ページ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-136">The layout page</span></span>

<span data-ttu-id="a0fbc-137">メニューのリンク ( **[RazorPagesMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="a0fbc-138">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="a0fbc-139">メニューのレイアウトは、*Pages/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a0fbc-140">*Pages/Shared/_Layout.cshtml* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a0fbc-141">[レイアウト](xref:mvc/views/layout) テンプレートを使用すると、HTML コンテナーのレイアウトを次のようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="a0fbc-142">1 つの場所で指定される。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-142">Specified in one place.</span></span>
* <span data-ttu-id="a0fbc-143">サイトの複数のページに適用される。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="a0fbc-144">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a0fbc-145">`RenderBody` は、ページ固有のビューがすべて表示されるプレースホルダーで、レイアウト ページに "*ラップ*" されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a0fbc-146">たとえば、 **[プライバシー]** リンクを選択すると、`RenderBody` メソッド内で *Pages/Privacy.cshtml* ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="a0fbc-147">ViewData とレイアウト</span><span class="sxs-lookup"><span data-stu-id="a0fbc-147">ViewData and layout</span></span>

<span data-ttu-id="a0fbc-148">*Pages/Movies/Index.cshtml* ファイルの次のマークアップを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="a0fbc-149">上の強調表示されたマークアップは、Razor の C# への移行例です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="a0fbc-150">`{` 文字と `}` 文字で C# コードのブロックを囲みます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="a0fbc-151">`PageModel` 基底クラスには `ViewData` 辞書プロパティが含まれており、これを使用してビューにデータを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="a0fbc-152">オブジェクトは、キー/値のパターンを使用して `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="a0fbc-153">上のサンプルでは、`"Title"` プロパティが `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="a0fbc-154">`"Title"` プロパティは *Pages/Shared/_Layout.cshtml* ファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a0fbc-155">次のマークアップは、 *_Layout.cshtml* ファイルの最初の数行を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="a0fbc-156">`@*Markup removed for brevity.*@` 行は Razor コメントです。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="a0fbc-157">HTML コメント (`<!-- -->`) とは異なり、Razor コメントはクライアントには送信されません。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="a0fbc-158">レイアウトの更新</span><span class="sxs-lookup"><span data-stu-id="a0fbc-158">Update the layout</span></span>

<span data-ttu-id="a0fbc-159">**RazorPagesMovie** ではなく **Movie** が表示されるように、*Pages/Shared/_Layout.cshtml* ファイルの `<title>` 要素を変更します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="a0fbc-160">*Pages/Shared/_Layout.cshtml* ファイルで次のアンカー要素を見つけます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="a0fbc-161">上の要素を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="a0fbc-162">上のアンカー要素は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="a0fbc-163">この場合は、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="a0fbc-164">`asp-page="/Movies/Index"` タグ ヘルパー属性と値で、`/Movies/Index` Razor ページへのリンクが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="a0fbc-165">`asp-area` 属性の値が空なので、リンクではこの区分が使用されていません。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="a0fbc-166">詳細については、[区分](xref:mvc/controllers/areas)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="a0fbc-167">変更内容を保存し、**RpMovie** リンクをクリックしてアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="a0fbc-168">問題がある場合は、GitHub の [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-168">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="a0fbc-169">その他のリンク ( **[Home]** 、 **[RpMovie]** 、 **[Create]** 、 **[Edit]** 、および **[Delete]** ) をテストします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="a0fbc-170">各ページで、ブラウザー タブで表示できるタイトルを設定します。ページをブックマークすると、ブックマークでタイトルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="a0fbc-171">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a0fbc-172">小数点にコンマ (",") を使い、英語 (米国) 以外の日付形式を使う英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="a0fbc-173">小数点のコンマの追加方法については、[こちらの GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-173">See this [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="a0fbc-174">`Layout` プロパティは *Pages/_ViewStart.cshtml* ファイルで設定されています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a0fbc-175">上のマークアップでは、*Pages* フォルダーの下のすべての Razor ファイルに対して、レイアウト ファイルを *Pages/Shared/_Layout.cshtml* に設定します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="a0fbc-176">詳細については、「[Layout](xref:razor-pages/index#layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="a0fbc-177">Create ページのモデル</span><span class="sxs-lookup"><span data-stu-id="a0fbc-177">The Create page model</span></span>

<span data-ttu-id="a0fbc-178">*Pages/Movies/Create.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="a0fbc-179">`OnGet` メソッドは、ページに必要な状態を初期化します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="a0fbc-180">[作成] ページには初期化する状態はないため、`Page` が返されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="a0fbc-181">このチュートリアルの後半では、状態を初期化する `OnGet` の例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="a0fbc-182">`Page` メソッドでは、*Create.cshtml* ページをレンダリングする `PageResult` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="a0fbc-183">`Movie` プロパティは `[BindProperty]` 属性を使用して、[モデル バインド](xref:mvc/models/model-binding)にオプトインします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="a0fbc-184">[作成] フォームでフォーム値が投稿されると、ASP.NET Core ランタイムが投稿された値を `Movie` モデルにバインドします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="a0fbc-185">`OnPostAsync` メソッドは、ページでフォーム データが投稿されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="a0fbc-186">モデル エラーがある場合は、投稿されたフォーム データと共にフォームが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="a0fbc-187">ほとんどのモデル エラーは、フォームが投稿される前にクライアント側でキャッチできます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="a0fbc-188">モデル エラーの例では、日付に変換できない日付フィールドの値が投稿されています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="a0fbc-189">クライアント側の検証とモデルの検証については、チュートリアルの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="a0fbc-190">モデル エラーがない場合、データは保存され、ブラウザーはインデックス ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="a0fbc-191">[作成] Razor ページ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-191">The Create Razor Page</span></span>

<span data-ttu-id="a0fbc-192">次のように、*Pages/Movies/Create.cshtml* Razor ページ ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0fbc-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0fbc-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0fbc-194">Visual Studio に、タグ ヘルパーで使用される独特な太字のフォントで次のタグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml ページの VS17 ビュー](page/_static/th3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0fbc-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0fbc-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0fbc-197">上記のマークアップには、次のタグ ヘルパーが示されています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a0fbc-198">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a0fbc-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a0fbc-199">Visual Studio に、タグ ヘルパーで使用される独特な太字のフォントで次のタグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="a0fbc-200">`<form method="post">` 要素は[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="a0fbc-201">フォーム タグ ヘルパーには自動的に[偽造防止トークン](xref:security/anti-request-forgery)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="a0fbc-202">スキャフォールディング エンジンは、次のような、(ID を除く) モデルの各フィールドの Razor マークアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="a0fbc-203">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` と `<span asp-validation-for`) には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="a0fbc-204">検証については、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="a0fbc-205">[ラベル タグ ヘルパー](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) は、`Title` プロパティのラベル キャプションと `for` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="a0fbc-206">[入力タグ ヘルパー](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="a0fbc-207">`<form method="post">` などのタグ ヘルパーについては、「[ASP.NET Core のタグ ヘルパー](xref:mvc/views/tag-helpers/intro)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0fbc-208">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a0fbc-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a0fbc-209">[前へ:モデルの追加](xref:tutorials/razor-pages/model)
> [次:データベース](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="a0fbc-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0fbc-210">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a0fbc-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a0fbc-211">このチュートリアルでは、[前のチュートリアル](xref:tutorials/razor-pages/model)でスキャフォールディングによって作成された Razor ページについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="a0fbc-212">サンプルを[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22)します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-212">[View or download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="a0fbc-213">[作成]、[削除]、[詳細]、および [編集] ページ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="a0fbc-214">次のように、*Pages/Movies/Index.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="a0fbc-215">Razor ページは `PageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="a0fbc-216">慣例により、`PageModel` 派生クラスは `<PageName>Model` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="a0fbc-217">コンストラクターは[依存性の注入](xref:fundamentals/dependency-injection)を使用して、`RazorPagesMovieContext` をページに追加します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="a0fbc-218">スキャフォールディングされたページではすべてこのパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="a0fbc-219">Entity Framework での非同期プログラミングの詳細については、「[非同期コード](xref:data/ef-rp/intro#asynchronous-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="a0fbc-220">ページに対して要求が行われると、`OnGetAsync` メソッドは Razor ページにムービーのリストを返します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="a0fbc-221">Razor ページで `OnGetAsync` または `OnGet` が呼び出され、ページの状態が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="a0fbc-222">この場合、`OnGetAsync` でムービーのリストを取得し、表示します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="a0fbc-223">`OnGet` が `void` を返す場合、または `OnGetAsync` が `Task` を返す場合、return メソッドは使用されません。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="a0fbc-224">戻り値の型が `IActionResult` または `Task<IActionResult>` の場合は、return ステートメントを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="a0fbc-225">たとえば、*Pages/Movies/Create.cshtml.cs* `OnPostAsync` メソッドでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="a0fbc-226">次のように、*Pages/Movies/Index.cshtml* Razor ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="a0fbc-227">Razor では、HTML から C# または Razor 固有のマークアップに移行できます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="a0fbc-228">`@` シンボルの後に [Razor で予約済みのキーワード](xref:mvc/views/razor#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。それ以外の場合は、C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="a0fbc-229">`@page` Razor ディレクティブはファイルを MVC アクションに分割します。これは、要求を処理できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="a0fbc-230">`@page` はページで最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a0fbc-231">`@page` は、Razor 固有のマークアップへの移行の例です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="a0fbc-232">詳細については、「[Razor syntax](xref:mvc/views/razor#razor-syntax)」 (Razor の構文) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="a0fbc-233">次の HTML ヘルパーで使用されるラムダ式を確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="a0fbc-234">`DisplayNameFor` HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="a0fbc-235">ラムダ式は評価されるのではなく検査されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="a0fbc-236">これは、`model`、`model.Movie`、または `model.Movie[0]` が `null` または空である場合、アクセス違反がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="a0fbc-237">ラムダ式が (`@Html.DisplayFor(modelItem => item.Title)` などを使用して) 評価される場合は、モデルのプロパティ値が評価されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="a0fbc-238">@model ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="a0fbc-239">`@model` ディレクティブは、Razor ページに渡されるモデルの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="a0fbc-240">前の例の `@model` 行は、Razor ページで `PageModel` 派生クラスを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="a0fbc-241">モデルは、ページの `@Html.DisplayNameFor` および `@Html.DisplayFor` [HTML ヘルパーで](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="a0fbc-242">レイアウト ページ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-242">The layout page</span></span>

<span data-ttu-id="a0fbc-243">メニューのリンク ( **[RazorPagesMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="a0fbc-244">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="a0fbc-245">メニューのレイアウトは、*Pages/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a0fbc-246">*Pages/Shared/_Layout.cshtml* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a0fbc-247">[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="a0fbc-248">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a0fbc-249">`RenderBody` は、作成したページ固有のビューがすべて表示されるプレースホルダーで、レイアウト ページに*ラップ*されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a0fbc-250">たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Pages/Privacy.cshtml** ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="a0fbc-251">ViewData とレイアウト</span><span class="sxs-lookup"><span data-stu-id="a0fbc-251">ViewData and layout</span></span>

<span data-ttu-id="a0fbc-252">*Pages/Movies/Index.cshtml* ファイルの次のコードを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="a0fbc-253">上の強調表示されたコードは、Razor の C# への移行例です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="a0fbc-254">`{` 文字と `}` 文字で C# コードのブロックを囲みます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="a0fbc-255">`PageModel` 基本クラスには `ViewData` 辞書プロパティがあり、これを使用して、ビューに渡すデータを追加できます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="a0fbc-256">キー/値のパターンを使用して、`ViewData` 辞書にオブジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="a0fbc-257">上のサンプルでは、"Title" プロパティが `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="a0fbc-258">"Title" プロパティは *Pages/Shared/_Layout.cshtml* ファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a0fbc-259">次のマークアップは、 *_Layout.cshtml* ファイルの最初の数行を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="a0fbc-260">`@*Markup removed for brevity.*@` の行は Razor コメントで、レイアウト ファイルには表示されません。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="a0fbc-261">HTML コメント (`<!-- -->`) とは異なり、Razor コメントはクライアントには送信されません。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="a0fbc-262">レイアウトの更新</span><span class="sxs-lookup"><span data-stu-id="a0fbc-262">Update the layout</span></span>

<span data-ttu-id="a0fbc-263">**RazorPagesMovie** ではなく **Movie** が表示されるように、*Pages/Shared/_Layout.cshtml* ファイルの `<title>` 要素を変更します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="a0fbc-264">*Pages/Shared/_Layout.cshtml* ファイルで次のアンカー要素を見つけます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="a0fbc-265">上の要素を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="a0fbc-266">上のアンカー要素は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="a0fbc-267">この場合は、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="a0fbc-268">`asp-page="/Movies/Index"` タグ ヘルパー属性と値で、`/Movies/Index` Razor ページへのリンクが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="a0fbc-269">`asp-area` 属性の値が空なので、リンクではこの区分が使用されていません。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="a0fbc-270">詳細については、[区分](xref:mvc/controllers/areas)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="a0fbc-271">変更内容を保存し、**RpMovie** リンクをクリックしてアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="a0fbc-272">問題がある場合は、GitHub の [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-272">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="a0fbc-273">その他のリンク ( **[Home]** 、 **[RpMovie]** 、 **[Create]** 、 **[Edit]** 、および **[Delete]** ) をテストします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="a0fbc-274">各ページで、ブラウザー タブで表示できるタイトルを設定します。ページをブックマークすると、ブックマークでタイトルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="a0fbc-275">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a0fbc-276">小数点にコンマ (",") を使い、英語 (米国) 以外の日付形式を使う英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="a0fbc-277">小数点のコンマの追加方法については、[こちらの GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-277">This [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="a0fbc-278">`Layout` プロパティは *Pages/_ViewStart.cshtml* ファイルで設定されています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a0fbc-279">上のマークアップでは、*Pages* フォルダーの下のすべての Razor ファイルに対して、レイアウト ファイルを *Pages/Shared/_Layout.cshtml* に設定します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="a0fbc-280">詳細については、「[Layout](xref:razor-pages/index#layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="a0fbc-281">Create ページのモデル</span><span class="sxs-lookup"><span data-stu-id="a0fbc-281">The Create page model</span></span>

<span data-ttu-id="a0fbc-282">*Pages/Movies/Create.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="a0fbc-283">`OnGet` メソッドは、ページに必要な状態を初期化します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="a0fbc-284">[作成] ページには初期化する状態はないため、`Page` が返されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="a0fbc-285">このチュートリアルで後ほど、`OnGet` メソッドの状態の初期化を確認できます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="a0fbc-286">`Page` メソッドでは、*Create.cshtml* ページをレンダリングする `PageResult` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="a0fbc-287">`Movie` プロパティは `[BindProperty]` 属性を使用して、[モデル バインド](xref:mvc/models/model-binding)にオプトインします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="a0fbc-288">[作成] フォームでフォーム値が投稿されると、ASP.NET Core ランタイムが投稿された値を `Movie` モデルにバインドします。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="a0fbc-289">`OnPostAsync` メソッドは、ページでフォーム データが投稿されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="a0fbc-290">モデル エラーがある場合は、投稿されたフォーム データと共にフォームが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="a0fbc-291">ほとんどのモデル エラーは、フォームが投稿される前にクライアント側でキャッチできます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="a0fbc-292">モデル エラーの例では、日付に変換できない日付フィールドの値が投稿されています。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="a0fbc-293">クライアント側の検証とモデルの検証については、チュートリアルの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="a0fbc-294">モデル エラーがない場合、データは保存され、ブラウザーはインデックス ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="a0fbc-295">[作成] Razor ページ</span><span class="sxs-lookup"><span data-stu-id="a0fbc-295">The Create Razor Page</span></span>

<span data-ttu-id="a0fbc-296">次のように、*Pages/Movies/Create.cshtml* Razor ページ ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0fbc-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0fbc-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0fbc-298">Visual Studio に、タグ ヘルパーで使用される独特な太字のフォントで `<form method="post">` タグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml ページの VS17 ビュー](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0fbc-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0fbc-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0fbc-301">`<form method="post">` などのタグ ヘルパーについては、「[ASP.NET Core のタグ ヘルパー](xref:mvc/views/tag-helpers/intro)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a0fbc-302">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a0fbc-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a0fbc-303">Visual Studio for Mac に、タグ ヘルパーで使用される独特な太字のフォントで `<form method="post">` タグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="a0fbc-304">`<form method="post">` 要素は[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="a0fbc-305">フォーム タグ ヘルパーには自動的に[偽造防止トークン](xref:security/anti-request-forgery)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="a0fbc-306">スキャフォールディング エンジンは、次のような、(ID を除く) モデルの各フィールドの Razor マークアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="a0fbc-307">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` と `<span asp-validation-for`) には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="a0fbc-308">検証については、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="a0fbc-309">[ラベル タグ ヘルパー](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) は、`Title` プロパティのラベル キャプションと `for` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="a0fbc-310">[入力タグ ヘルパー](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="a0fbc-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0fbc-311">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a0fbc-311">Additional resources</span></span>

* [<span data-ttu-id="a0fbc-312">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="a0fbc-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="a0fbc-313">[前へ:モデルの追加](xref:tutorials/razor-pages/model)
> [次:データベース](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="a0fbc-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
