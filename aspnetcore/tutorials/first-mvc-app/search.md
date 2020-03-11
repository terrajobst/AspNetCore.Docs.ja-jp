---
title: ASP.NET Core MVC アプリへの検索の追加
author: rick-anderson
description: 基本的な ASP.NET Core MVC アプリに検索を追加する方法を示します
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648830"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="297e0-103">ASP.NET Core MVC アプリへの検索の追加</span><span class="sxs-lookup"><span data-stu-id="297e0-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="297e0-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="297e0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="297e0-105">このセクションでは、検索機能を `Index` アクション メソッドに追加して、*ジャンル*または*名前*でムービーを検索できるようにします。</span><span class="sxs-lookup"><span data-stu-id="297e0-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="297e0-106">次のコードを使用して、*Controllers/MoviesController.cs* 内で見つかった `Index` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="297e0-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="297e0-107">`Index` アクション メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/standard/using-linq) クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="297e0-108">このクエリはこの時点では定義される*だけ*で、データベースに対して**実行されていません**。</span><span class="sxs-lookup"><span data-stu-id="297e0-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="297e0-109">`searchString` パラメーターに文字列が含まれる場合、検索文字列の値でフィルターするようにムービー クエリが変更されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="297e0-110">上の `s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。</span><span class="sxs-lookup"><span data-stu-id="297e0-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="297e0-111">ラムダは、メソッド ベースの [LINQ](/dotnet/standard/using-linq) クエリで、[Where](/dotnet/api/system.linq.enumerable.where) メソッドや `Contains` (上のコードで使用されている) など、標準クエリ演算子メソッドの引数として使用されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="297e0-112">LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。</span><span class="sxs-lookup"><span data-stu-id="297e0-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="297e0-113">クエリ実行は先送りされます。</span><span class="sxs-lookup"><span data-stu-id="297e0-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="297e0-114">つまり、その具体値が実際に繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延期されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="297e0-115">クエリの遅延実行の詳細については、「[クエリの実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="297e0-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="297e0-116">メモ:[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは、上記の C# コードではなく、データベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="297e0-117">クエリの大文字と小文字の区別は、データベースや照合順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="297e0-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="297e0-118">SQL Server では、[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) は大文字と小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="297e0-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="297e0-119">SQLite では、既定の照合順序で、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="297e0-120">`/Movies/Index` に移動します。</span><span class="sxs-lookup"><span data-stu-id="297e0-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="297e0-121">`?searchString=Ghost` などのクエリ文字列を URL に追加します。</span><span class="sxs-lookup"><span data-stu-id="297e0-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="297e0-122">フィルターされたムービーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-122">The filtered movies are displayed.</span></span>

![インデックス ビュー](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="297e0-124">`id` という名前のパラメーターを使用するために `Index` メソッドの署名を変更すると、`id` パラメーターは、*Startup.cs* で設定されている既定ルートの省略可能な `{id}` プレースホルダーと一致するようになります。</span><span class="sxs-lookup"><span data-stu-id="297e0-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="297e0-125">パラメーターを `id` に変更します。`searchString` がすべて `id` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="297e0-126">上記の `Index` メソッド:</span><span class="sxs-lookup"><span data-stu-id="297e0-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="297e0-127">`id` パラメーターで更新された `Index` メソッド:</span><span class="sxs-lookup"><span data-stu-id="297e0-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="297e0-128">これで、クエリ文字列の値ではなく、ルート データ (URL セグメント) として検索タイトルを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="297e0-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![ghost という単語が URL に追加された索引ビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービーリストが返されています。](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="297e0-130">ただし、ユーザーがムービーを検索するたびに URL の変更を求めることはできません。</span><span class="sxs-lookup"><span data-stu-id="297e0-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="297e0-131">そのため、ここでは UI 要素を追加して、ムービーをフィルターできるようにします。</span><span class="sxs-lookup"><span data-stu-id="297e0-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="297e0-132">ルート バインドされた `ID` パラメーターを渡す方法をテストするために `Index` メソッドの署名を変更した場合は、`searchString` という名前のパラメーターを受け取るように署名を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="297e0-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="297e0-133">*Views/Movies/Index.cshtml* ファイルを開き、以下の強調表示されている `<form>` マークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="297e0-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="297e0-134">HTML `<form>` タグでは[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)が使用されるため、フォームを送信するときに、フィルター文字列がムービー コントローラーの `Index` アクションに投稿されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="297e0-135">変更内容を保存してから、フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="297e0-135">Save your changes and then test the filter.</span></span>

![タイトル フィルター テキストボックスに ghost という単語が入力されたインデックス ビュー](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="297e0-137">予想どおり、`Index` メソッドの `[HttpPost]` オーバーロードはありません。</span><span class="sxs-lookup"><span data-stu-id="297e0-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="297e0-138">メソッドではデータをフィルターするだけで、アプリの状態を変更しないため、オーバーロードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="297e0-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="297e0-139">以下の `[HttpPost] Index` メソッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="297e0-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="297e0-140">`notUsed` パラメーターは、`Index` メソッドのオーバーロードを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="297e0-141">これについては、チュートリアルの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="297e0-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="297e0-142">このメソッドを追加すると、アクション呼び出し元が `[HttpPost] Index` メソッドと一致し、`[HttpPost] Index` メソッドが以下のイメージのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![From HttpPost Index: filter on ghost というアプリケーション応答を示すブラウザー ウィンドウ](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="297e0-144">ただし、この `[HttpPost]` バージョンの `Index` メソッドを追加しても、実装方法は制限されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="297e0-145">たとえば、特定の検索をブックマークするか、友だちにリンクを送信し、友だちがそれをクリックしてムービーのフィルターされた同じリストを表示できるようにするとします。</span><span class="sxs-lookup"><span data-stu-id="297e0-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="297e0-146">HTTP POST 要求の URL は、GET 要求の URL (localhost:{PORT}/Movies/Index) と同じであり、URL には検索情報がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="297e0-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="297e0-147">検索文字列情報は、[フォーム フィールド値](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)としてサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="297e0-148">ブラウザーの開発者ツールまたは優れた [Fiddler ツール](https://www.telerik.com/fiddler)を使用して、これを確認できます。</span><span class="sxs-lookup"><span data-stu-id="297e0-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="297e0-149">次のイメージは、Chrome ブラウザーの開発者ツールを示しています。</span><span class="sxs-lookup"><span data-stu-id="297e0-149">The image below shows the Chrome browser Developer tools:</span></span>

![searchString 値が ghost の要求本文を示す、Microsoft Edge の開発者ツールの [ネットワーク] タブ](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="297e0-151">要求本文に検索パラメーターと [XSRF](xref:security/anti-request-forgery) トークンが表示されています。</span><span class="sxs-lookup"><span data-stu-id="297e0-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="297e0-152">前のチュートリアルで説明したように、[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)では [XSRF](xref:security/anti-request-forgery) 偽造防止トークンが生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="297e0-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="297e0-153">ここではデータを変更しないため、コントローラー メソッドでトークンを検証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="297e0-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="297e0-154">検索パラメーターが URL ではなく、要求本文にあるため、その検索情報をキャプチャして、ブックマークしたり、他のユーザーと共有したりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="297e0-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="297e0-155">この問題を解決するには、*Views/Movies/Index. cshtml* ファイルに存在する要求が `HTTP GET` であることを指定します。</span><span class="sxs-lookup"><span data-stu-id="297e0-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="297e0-156">ここで検索を送信すると、URL に検索クエリ文字列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="297e0-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="297e0-157">`HttpPost Index` メソッドがある場合でも、検索時には `HttpGet Index` アクション メソッドにも移動します。</span><span class="sxs-lookup"><span data-stu-id="297e0-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![URL に searchString=ghost が表示されたブラウザー ウィンドウ。返された Ghostbusters および Ghostbusters 2 というムービーには ghost という単語が含まれています](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="297e0-159">次のマークアップは `form` タグの変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="297e0-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="297e0-160">ジャンルによる検索の追加</span><span class="sxs-lookup"><span data-stu-id="297e0-160">Add Search by genre</span></span>

<span data-ttu-id="297e0-161">次の `MovieGenreViewModel` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="297e0-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="297e0-162">ムービージャンルのビュー モデルには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="297e0-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="297e0-163">ムービーのリスト。</span><span class="sxs-lookup"><span data-stu-id="297e0-163">A list of movies.</span></span>
* <span data-ttu-id="297e0-164">ジャンルのリストを含む `SelectList`。</span><span class="sxs-lookup"><span data-stu-id="297e0-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="297e0-165">これにより、ユーザーは一覧からジャンルを選択できます。</span><span class="sxs-lookup"><span data-stu-id="297e0-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="297e0-166">選択されたジャンルを含む、`MovieGenre`。</span><span class="sxs-lookup"><span data-stu-id="297e0-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="297e0-167">ユーザーが検索テキスト ボックスに入力したテキストが含まれる `SearchString`。</span><span class="sxs-lookup"><span data-stu-id="297e0-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="297e0-168">`MoviesController.cs` の `Index` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="297e0-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="297e0-169">次のコードは、データベースからすべてのジャンルを取得する `LINQ` クエリです。</span><span class="sxs-lookup"><span data-stu-id="297e0-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="297e0-170">ジャンルの `SelectList` は、個々のジャンルを投影して作成します (選択リストでジャンルが重複しないようにします)。</span><span class="sxs-lookup"><span data-stu-id="297e0-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="297e0-171">ユーザーが項目を検索すると、検索値が検索ボックスに保持されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="297e0-172">インデックス ビューへのジャンルによる検索の追加</span><span class="sxs-lookup"><span data-stu-id="297e0-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="297e0-173">次のように、*Views/Movies/* で見つかった `Index.cshtml` を更新します。</span><span class="sxs-lookup"><span data-stu-id="297e0-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="297e0-174">次の HTML ヘルパーで使用されるラムダ式を確認します。</span><span class="sxs-lookup"><span data-stu-id="297e0-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="297e0-175">上のコードでは、`DisplayNameFor` HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。</span><span class="sxs-lookup"><span data-stu-id="297e0-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="297e0-176">ラムダ式は評価されるのではなく、検査されるため、`model`、`model.Movies`、または `model.Movies[0]` が `null` または空である場合にアクセス違反が発生することはありません。</span><span class="sxs-lookup"><span data-stu-id="297e0-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="297e0-177">ラムダ式が評価される場合 (`@Html.DisplayFor(modelItem => item.Title)` など)、モデルのプロパティ値が評価されます。</span><span class="sxs-lookup"><span data-stu-id="297e0-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="297e0-178">ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="297e0-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2 の結果を示すブラウザー ウィンドウ](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="297e0-180">[前へ](controller-methods-views.md)
> [次へ](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="297e0-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
