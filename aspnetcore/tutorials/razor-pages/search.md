---
title: ASP.NET Core Razor ページへの検索の追加
author: rick-anderson
description: ASP.NET Core Razor ページに検索を追加する方法を紹介します
ms.author: riande
ms.date: 7/23/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: d1aa3f914ebcab4d095b6fca1dac3cf44855d516
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483288"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="27699-103">ASP.NET Core Razor ページへの検索の追加</span><span class="sxs-lookup"><span data-stu-id="27699-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="27699-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="27699-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="27699-105">次のセクションでは、*ジャンル*または*名前*による映画検索が追加されます。</span><span class="sxs-lookup"><span data-stu-id="27699-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="27699-106">強調表示されている次のプロパティを *Pages/Movies/Index.cshtml.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="27699-107">`SearchString`: ユーザーが検索テキスト ボックスに入力したテキストが含まれる。</span><span class="sxs-lookup"><span data-stu-id="27699-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="27699-108">`SearchString` は [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 属性で修飾されています。</span><span class="sxs-lookup"><span data-stu-id="27699-108">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="27699-109">`[BindProperty]` により、プロパティと同じ名前に基づきフォーム値とクエリ文字列がバインドされます。</span><span class="sxs-lookup"><span data-stu-id="27699-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="27699-110">GET 要求でのバインドには `(SupportsGet = true)` が必要です。</span><span class="sxs-lookup"><span data-stu-id="27699-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="27699-111">`Genres`: ジャンル一覧が含まれる。</span><span class="sxs-lookup"><span data-stu-id="27699-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="27699-112">`Genres` により、ユーザーは一覧からジャンルを選択できます。</span><span class="sxs-lookup"><span data-stu-id="27699-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="27699-113">`SelectList` には `using Microsoft.AspNetCore.Mvc.Rendering;` が必要です。</span><span class="sxs-lookup"><span data-stu-id="27699-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="27699-114">`MovieGenre`: "Western (西部劇)" など、ユーザーが選択する特定のジャンルが含まれる。</span><span class="sxs-lookup"><span data-stu-id="27699-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="27699-115">`Genres` と `MovieGenre` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="27699-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="27699-116">索引ページの `OnGetAsync` メソッドを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="27699-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="27699-117">`OnGetAsync` メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="27699-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="27699-118">このクエリはこの時点では定義される*だけ*で、データベースに対して**実行されていません**。</span><span class="sxs-lookup"><span data-stu-id="27699-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="27699-119">`SearchString` プロパティが null でも空でもない場合、検索文字列で絞り込むようにムービークエリが変更されます。</span><span class="sxs-lookup"><span data-stu-id="27699-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="27699-120">`s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。</span><span class="sxs-lookup"><span data-stu-id="27699-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="27699-121">ラムダは、メソッド ベースの [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリで、[Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) メソッドや `Contains` (先のコードで使用されています) など、標準クエリ演算子メソッドの引数として使用されます。</span><span class="sxs-lookup"><span data-stu-id="27699-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="27699-122">LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。</span><span class="sxs-lookup"><span data-stu-id="27699-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="27699-123">クエリ実行は先送りされます。</span><span class="sxs-lookup"><span data-stu-id="27699-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="27699-124">つまり、その具体値が繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延ばされます。</span><span class="sxs-lookup"><span data-stu-id="27699-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="27699-125">詳細については、「[クエリ実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="27699-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="27699-126">**注:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは C# コードではなく、データベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="27699-126">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="27699-127">クエリの大文字と小文字の区別は、データベースや照合順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="27699-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="27699-128">SQL Server では、`Contains` は大文字/小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="27699-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="27699-129">SQLite では、既定の照合順序で、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="27699-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="27699-130">ムービーページに移動し、`?searchString=Ghost` のようなクエリ文字列を URL に追加します (例: `https://localhost:5001/Movies?searchString=Ghost`)。</span><span class="sxs-lookup"><span data-stu-id="27699-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="27699-131">フィルターされたムービーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="27699-131">The filtered movies are displayed.</span></span>

![インデックス ビュー](search/_static/ghost.png)

<span data-ttu-id="27699-133">次のルート テンプレートが索引ページに追加される場合、検索文字列を URL セグメントとして渡すことができます (例: `https://localhost:5001/Movies/Ghost`)。</span><span class="sxs-lookup"><span data-stu-id="27699-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="27699-134">先のルート制約では、クエリ文字列値の代わりに、ルート データ (URL セグメント) として題名を検索できます。</span><span class="sxs-lookup"><span data-stu-id="27699-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="27699-135">`"{searchString?}"` の `?` は、これが任意のルート パラメーターであることを意味します。</span><span class="sxs-lookup"><span data-stu-id="27699-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![ghost という単語が URL に追加された索引ビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービーリストが返されています。](search/_static/g2.png)

<span data-ttu-id="27699-137">ASP.NET Core ランタイムでは[モデル バインド](xref:mvc/models/model-binding)を使用し、クエリ文字列 (`?searchString=Ghost`) またはルート データ (`https://localhost:5001/Movies/Ghost`) から `SearchString` プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="27699-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="27699-138">モデル バインドでは、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="27699-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="27699-139">ただし、URL を変更してムービーを検索することをユーザーに求めることはできません。</span><span class="sxs-lookup"><span data-stu-id="27699-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="27699-140">この手順では、ムービーを絞り込むための UI を追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="27699-141">ルート制約 `"{searchString?}"` を追加した場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="27699-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="27699-142">*Pages/Movies/Index.cshtml* ファイルを開き、次のコードで強調表示されている `<form>` マークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="27699-143">HTML `<form>` タグでは、次の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="27699-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="27699-144">[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="27699-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="27699-145">フォームが提出されると、フィルター文字列がクエリ文字列経由で*ページ/ムービー/索引*ページに送信されます。</span><span class="sxs-lookup"><span data-stu-id="27699-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="27699-146">入力タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="27699-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="27699-147">変更を保存し、フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="27699-147">Save the changes and test the filter.</span></span>

![タイトル フィルター テキストボックスに ghost という単語が入力されたインデックス ビュー](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="27699-149">ジャンルで検索する</span><span class="sxs-lookup"><span data-stu-id="27699-149">Search by genre</span></span>

<span data-ttu-id="27699-150">`OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="27699-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="27699-151">次のコードは、データベースからすべてのジャンルを取得する LINQ クエリです。</span><span class="sxs-lookup"><span data-stu-id="27699-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="27699-152">ジャンルの `SelectList` は、別個のジャンルを推定することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="27699-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="27699-153">ジャンル検索を Razor ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="27699-154">*Index.cshtml* を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="27699-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="27699-155">ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="27699-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="27699-156">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="27699-156">Additional resources</span></span>

* [<span data-ttu-id="27699-157">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="27699-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="27699-158">[前へ:ページの更新](xref:tutorials/razor-pages/da1)
> [次: 新しいフィールドの追加](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="27699-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="27699-159">次のセクションでは、*ジャンル*または*名前*による映画検索が追加されます。</span><span class="sxs-lookup"><span data-stu-id="27699-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="27699-160">強調表示されている次のプロパティを *Pages/Movies/Index.cshtml.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="27699-161">`SearchString`: ユーザーが検索テキスト ボックスに入力したテキストが含まれる。</span><span class="sxs-lookup"><span data-stu-id="27699-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="27699-162">`SearchString` は [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 属性で修飾されています。</span><span class="sxs-lookup"><span data-stu-id="27699-162">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="27699-163">`[BindProperty]` により、プロパティと同じ名前に基づきフォーム値とクエリ文字列がバインドされます。</span><span class="sxs-lookup"><span data-stu-id="27699-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="27699-164">GET 要求でのバインドには `(SupportsGet = true)` が必要です。</span><span class="sxs-lookup"><span data-stu-id="27699-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="27699-165">`Genres`: ジャンル一覧が含まれる。</span><span class="sxs-lookup"><span data-stu-id="27699-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="27699-166">`Genres` により、ユーザーは一覧からジャンルを選択できます。</span><span class="sxs-lookup"><span data-stu-id="27699-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="27699-167">`SelectList` には `using Microsoft.AspNetCore.Mvc.Rendering;` が必要です。</span><span class="sxs-lookup"><span data-stu-id="27699-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="27699-168">`MovieGenre`: "Western (西部劇)" など、ユーザーが選択する特定のジャンルが含まれる。</span><span class="sxs-lookup"><span data-stu-id="27699-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="27699-169">`Genres` と `MovieGenre` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="27699-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="27699-170">索引ページの `OnGetAsync` メソッドを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="27699-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="27699-171">`OnGetAsync` メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="27699-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="27699-172">このクエリはこの時点では定義される*だけ*で、データベースに対して**実行されていません**。</span><span class="sxs-lookup"><span data-stu-id="27699-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="27699-173">`SearchString` プロパティが null でも空でもない場合、検索文字列で絞り込むようにムービークエリが変更されます。</span><span class="sxs-lookup"><span data-stu-id="27699-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="27699-174">`s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。</span><span class="sxs-lookup"><span data-stu-id="27699-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="27699-175">ラムダは、メソッド ベースの [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリで、[Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) メソッドや `Contains` (先のコードで使用されています) など、標準クエリ演算子メソッドの引数として使用されます。</span><span class="sxs-lookup"><span data-stu-id="27699-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="27699-176">LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。</span><span class="sxs-lookup"><span data-stu-id="27699-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="27699-177">クエリ実行は先送りされます。</span><span class="sxs-lookup"><span data-stu-id="27699-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="27699-178">つまり、その具体値が繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延ばされます。</span><span class="sxs-lookup"><span data-stu-id="27699-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="27699-179">詳細については、「[クエリ実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="27699-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="27699-180">**注:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは C# コードではなく、データベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="27699-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="27699-181">クエリの大文字と小文字の区別は、データベースや照合順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="27699-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="27699-182">SQL Server では、`Contains` は大文字/小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="27699-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="27699-183">SQLite では、既定の照合順序で、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="27699-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="27699-184">ムービーページに移動し、`?searchString=Ghost` のようなクエリ文字列を URL に追加します (例: `https://localhost:5001/Movies?searchString=Ghost`)。</span><span class="sxs-lookup"><span data-stu-id="27699-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="27699-185">フィルターされたムービーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="27699-185">The filtered movies are displayed.</span></span>

![インデックス ビュー](search/_static/ghost.png)

<span data-ttu-id="27699-187">次のルート テンプレートが索引ページに追加される場合、検索文字列を URL セグメントとして渡すことができます (例: `https://localhost:5001/Movies/Ghost`)。</span><span class="sxs-lookup"><span data-stu-id="27699-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="27699-188">先のルート制約では、クエリ文字列値の代わりに、ルート データ (URL セグメント) として題名を検索できます。</span><span class="sxs-lookup"><span data-stu-id="27699-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="27699-189">`"{searchString?}"` の `?` は、これが任意のルート パラメーターであることを意味します。</span><span class="sxs-lookup"><span data-stu-id="27699-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![ghost という単語が URL に追加された索引ビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービーリストが返されています。](search/_static/g2.png)

<span data-ttu-id="27699-191">ASP.NET Core ランタイムでは[モデル バインド](xref:mvc/models/model-binding)を使用し、クエリ文字列 (`?searchString=Ghost`) またはルート データ (`https://localhost:5001/Movies/Ghost`) から `SearchString` プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="27699-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="27699-192">モデル バインドでは、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="27699-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="27699-193">ただし、URL を変更してムービーを検索することをユーザーに求めることはできません。</span><span class="sxs-lookup"><span data-stu-id="27699-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="27699-194">この手順では、ムービーを絞り込むための UI を追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="27699-195">ルート制約 `"{searchString?}"` を追加した場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="27699-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="27699-196">*Pages/Movies/Index.cshtml* ファイルを開き、次のコードで強調表示されている `<form>` マークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="27699-197">HTML `<form>` タグでは、次の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="27699-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="27699-198">[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="27699-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="27699-199">フォームが提出されると、フィルター文字列がクエリ文字列経由で*ページ/ムービー/索引*ページに送信されます。</span><span class="sxs-lookup"><span data-stu-id="27699-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="27699-200">入力タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="27699-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="27699-201">変更を保存し、フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="27699-201">Save the changes and test the filter.</span></span>

![タイトル フィルター テキストボックスに ghost という単語が入力されたインデックス ビュー](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="27699-203">ジャンルで検索する</span><span class="sxs-lookup"><span data-stu-id="27699-203">Search by genre</span></span>

<span data-ttu-id="27699-204">`OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="27699-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="27699-205">次のコードは、データベースからすべてのジャンルを取得する LINQ クエリです。</span><span class="sxs-lookup"><span data-stu-id="27699-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="27699-206">ジャンルの `SelectList` は、別個のジャンルを推定することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="27699-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="27699-207">ジャンル検索を Razor ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="27699-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="27699-208">*Index.cshtml* を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="27699-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="27699-209">ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="27699-209">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="27699-210">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="27699-210">Additional resources</span></span>

* [<span data-ttu-id="27699-211">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="27699-211">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="27699-212">[前へ:ページの更新](xref:tutorials/razor-pages/da1)
> [次: 新しいフィールドの追加](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="27699-212">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end