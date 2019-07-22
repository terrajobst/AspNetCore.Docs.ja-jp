---
title: ASP.NET Core MVC アプリへの検索の追加
author: rick-anderson
description: 基本的な ASP.NET Core MVC アプリに検索を追加する方法を示します
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: fbec03d71e247c58fb5968290c4baf6b28120e1c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815064"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC アプリへの検索の追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このセクションでは、検索機能を `Index` アクション メソッドに追加して、*ジャンル*または*名前*でムービーを検索できるようにします。

`Index` メソッドを次のコードで更新します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

`Index` アクション メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/standard/using-linq) クエリが作成されます。

```csharp
var movies = from m in _context.Movie
             select m;
```

このクエリはこの時点では定義される*だけ*で、データベースに対して**実行されていません**。

`searchString` パラメーターに文字列が含まれる場合、検索文字列の値でフィルターするようにムービー クエリが変更されます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

上の `s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。 ラムダは、メソッド ベースの [LINQ](/dotnet/standard/using-linq) クエリで、[Where](/dotnet/api/system.linq.enumerable.where) メソッドや `Contains` (上のコードで使用されている) など、標準クエリ演算子メソッドの引数として使用されます。 LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。 クエリ実行は先送りされます。  つまり、その具体値が実際に繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延期されます。 クエリの遅延実行の詳細については、「[クエリの実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。

メモ:[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは、上記の C# コードではなく、データベースで実行されます。 クエリの大文字と小文字の区別は、データベースや照合順序に依存します。 SQL Server では、[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) は大文字と小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。 SQLite では、既定の照合順序で、大文字と小文字が区別されます。

`/Movies/Index` に移動します。 `?searchString=Ghost` などのクエリ文字列を URL に追加します。 フィルターされたムービーが表示されます。

![インデックス ビュー](~/tutorials/first-mvc-app/search/_static/ghost.png)

`id` という名前のパラメーターを使用するために `Index` メソッドの署名を変更すると、`id` パラメーターは、*Startup.cs* で設定されている既定ルートの省略可能な `{id}` プレースホルダーと一致するようになります。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

パラメーターを `id` に変更します。`searchString` がすべて `id` に変更されます。

上記の `Index` メソッド:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

`id` パラメーターで更新された `Index` メソッド:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

これで、クエリ文字列の値ではなく、ルート データ (URL セグメント) として検索タイトルを渡すことができます。

![ghost という単語が URL に追加された索引ビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービーリストが返されています。](~/tutorials/first-mvc-app/search/_static/g2.png)

ただし、ユーザーがムービーを検索するたびに URL の変更を求めることはできません。 そのため、ここでは UI 要素を追加して、ムービーをフィルターできるようにします。 ルート バインドされた `ID` パラメーターを渡す方法をテストするために `Index` メソッドの署名を変更した場合は、`searchString` という名前のパラメーターを受け取るように署名を元に戻します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

*Views/Movies/Index.cshtml* ファイルを開き、以下の強調表示されている `<form>` マークアップを追加します。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

HTML `<form>` タグでは[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)が使用されるため、フォームを送信するときに、フィルター文字列がムービー コントローラーの `Index` アクションに投稿されます。 変更内容を保存してから、フィルターをテストします。

![タイトル フィルター テキストボックスに ghost という単語が入力されたインデックス ビュー](~/tutorials/first-mvc-app/search/_static/filter.png)

予想どおり、`Index` メソッドの `[HttpPost]` オーバーロードはありません。 メソッドではデータをフィルターするだけで、アプリの状態を変更しないため、オーバーロードは必要ありません。

以下の `[HttpPost] Index` メソッドを追加できます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` パラメーターは、`Index` メソッドのオーバーロードを作成するために使用されます。 これについては、チュートリアルの後半で説明します。

このメソッドを追加すると、アクション呼び出し元が `[HttpPost] Index` メソッドと一致し、`[HttpPost] Index` メソッドが以下のイメージのように実行されます。

![From HttpPost Index: filter on ghost というアプリケーション応答を示すブラウザー ウィンドウ](~/tutorials/first-mvc-app/search/_static/fo.png)

ただし、この `[HttpPost]` バージョンの `Index` メソッドを追加しても、実装方法は制限されます。 たとえば、特定の検索をブックマークするか、友だちにリンクを送信し、友だちがそれをクリックしてムービーのフィルターされた同じリストを表示できるようにするとします。 HTTP POST 要求の URL は、GET 要求の URL (localhost:xxxxx/Movies/Index) と同じであり、URL には検索情報がないことに注意してください。 検索文字列情報は、[フォーム フィールド値](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)としてサーバーに送信されます。 ブラウザーの開発者ツールまたは優れた [Fiddler ツール](https://www.telerik.com/fiddler)を使用して、これを確認できます。 次のイメージは、Chrome ブラウザーの開発者ツールを示しています。

![searchString 値が ghost の要求本文を示す、Microsoft Edge の開発者ツールの [ネットワーク] タブ](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

要求本文に検索パラメーターと [XSRF](xref:security/anti-request-forgery) トークンが表示されています。 前のチュートリアルで説明したように、[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)では [XSRF](xref:security/anti-request-forgery) 偽造防止トークンが生成されることに注意してください。 ここではデータを変更しないため、コントローラー メソッドでトークンを検証する必要はありません。

検索パラメーターが URL ではなく、要求本文にあるため、その検索情報をキャプチャして、ブックマークしたり、他のユーザーと共有したりすることはできません。 `HTTP GET` の要求を指定して、これを解決します。

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

ここで検索を送信すると、URL に検索クエリ文字列が含まれます。 `HttpPost Index` メソッドがある場合でも、検索時には `HttpGet Index` アクション メソッドにも移動します。

![URL に searchString=ghost が表示されたブラウザー ウィンドウ。返された Ghostbusters および Ghostbusters 2 というムービーには ghost という単語が含まれています](~/tutorials/first-mvc-app/search/_static/search_get.png)

次のマークアップは `form` タグの変更を示しています。

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="add-search-by-genre"></a>ジャンルによる検索の追加

次の `MovieGenreViewModel` クラスを *Models* フォルダーに追加します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

ムービージャンルのビュー モデルには以下が含まれます。

* ムービーのリスト。
* ジャンルのリストを含む `SelectList`。 これにより、ユーザーは一覧からジャンルを選択できます。
* 選択されたジャンルを含む、`MovieGenre`。
* ユーザーが検索テキスト ボックスに入力したテキストが含まれる `SearchString`。

`MoviesController.cs` の `Index` メソッドを次のコードに置き換えます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

次のコードは、データベースからすべてのジャンルを取得する `LINQ` クエリです。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

ジャンルの `SelectList` は、個々のジャンルを投影して作成します (選択リストでジャンルが重複しないようにします)。

ユーザーが項目を検索すると、検索値が検索ボックスに保持されます。

## <a name="add-search-by-genre-to-the-index-view"></a>インデックス ビューへのジャンルによる検索の追加

次のように `Index.cshtml` を更新します。

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

次の HTML ヘルパーで使用されるラムダ式を確認します。

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

上のコードでは、`DisplayNameFor` HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。 ラムダ式は評価されるのではなく、検査されるため、`model`、`model.Movies`、または `model.Movies[0]` が `null` または空である場合にアクセス違反が発生することはありません。 ラムダ式が評価される場合 (`@Html.DisplayFor(modelItem => item.Title)` など)、モデルのプロパティ値が評価されます。

ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。

![https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2 の結果を示すブラウザー ウィンドウ](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [前へ](controller-methods-views.md)
> [次へ](new-field.md)
