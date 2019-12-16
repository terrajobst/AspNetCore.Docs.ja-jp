---
title: ASP.NET Core Razor ページへの検索の追加
author: rick-anderson
description: ASP.NET Core Razor ページに検索を追加する方法を紹介します
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8228207b0f37a6923b29891ac3115dd0be115501
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881326"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a>ASP.NET Core Razor ページへの検索の追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

次のセクションでは、*ジャンル*または*名前*による映画検索が追加されます。

強調表示されている次のプロパティを *Pages/Movies/Index.cshtml.cs* に追加します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: ユーザーが検索テキスト ボックスに入力したテキストが含まれる。 `SearchString` には、[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 属性があります。 `[BindProperty]` により、プロパティと同じ名前に基づきフォーム値とクエリ文字列がバインドされます。 GET 要求でのバインドには `(SupportsGet = true)` が必要です。
* `Genres`: ジャンル一覧が含まれる。 `Genres` により、ユーザーは一覧からジャンルを選択できます。 `SelectList` には `using Microsoft.AspNetCore.Mvc.Rendering;` が必要です。
* `MovieGenre`: "Western (西部劇)" など、ユーザーが選択する特定のジャンルが含まれる。
* `Genres` と `MovieGenre` は、このチュートリアルで後述します。

[!INCLUDE[](~/includes/bind-get.md)]

索引ページの `OnGetAsync` メソッドを次のコードに変更します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリが作成されます。

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

このクエリはこの時点では定義される*だけ*で、データベースに対して**実行されていません**。

`SearchString` プロパティが null でも空でもない場合、検索文字列で絞り込むようにムービークエリが変更されます。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。 ラムダは、メソッド ベースの [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリで、[Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) メソッドや `Contains` (先のコードで使用されています) など、標準クエリ演算子メソッドの引数として使用されます。 LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。 クエリ実行は先送りされます。 つまり、その具体値が繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延ばされます。 詳細については、「[クエリ実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。

> [!NOTE]
> [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは C# コードではなく、データベースで実行されます。 クエリの大文字と小文字の区別は、データベースや照合順序に依存します。 SQL Server では、`Contains` は大文字/小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。 SQLite では、既定の照合順序で、大文字と小文字が区別されます。

ムービーページに移動し、`?searchString=Ghost` のようなクエリ文字列を URL に追加します (例: `https://localhost:5001/Movies?searchString=Ghost`)。 フィルターされたムービーが表示されます。

![インデックス ビュー](search/_static/ghost.png)

次のルート テンプレートが索引ページに追加される場合、検索文字列を URL セグメントとして渡すことができます (例: `https://localhost:5001/Movies/Ghost`)。

```cshtml
@page "{searchString?}"
```

先のルート制約では、クエリ文字列値の代わりに、ルート データ (URL セグメント) として題名を検索できます。  `"{searchString?}"` の `?` は、これが任意のルート パラメーターであることを意味します。

![ghost という単語が URL に追加された索引ビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービーリストが返されています。](search/_static/g2.png)

ASP.NET Core ランタイムでは[モデル バインド](xref:mvc/models/model-binding)を使用し、クエリ文字列 (`?searchString=Ghost`) またはルート データ (`https://localhost:5001/Movies/Ghost`) から `SearchString` プロパティの値が設定されます。 モデル バインドでは、大文字と小文字が区別されません。

ただし、URL を変更してムービーを検索することをユーザーに求めることはできません。 この手順では、ムービーを絞り込むための UI を追加します。 ルート制約 `"{searchString?}"` を追加した場合、それを削除します。

*Pages/Movies/Index.cshtml* ファイルを開き、次のコードで強調表示されている `<form>` マークアップを追加します。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` タグでは、次の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)が使用されます。

* [フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper) フォームが提出されると、フィルター文字列がクエリ文字列経由で*ページ/ムービー/索引*ページに送信されます。
* [入力タグ ヘルパー](xref:mvc/views/working-with-forms#the-input-tag-helper)

変更を保存し、フィルターをテストします。

![タイトル フィルター テキストボックスに ghost という単語が入力されたインデックス ビュー](search/_static/filter.png)

## <a name="search-by-genre"></a>ジャンルで検索する

`OnGetAsync` メソッドを次のコードで更新します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

次のコードは、データベースからすべてのジャンルを取得する LINQ クエリです。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

ジャンルの `SelectList` は、別個のジャンルを推定することで作成されます。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>ジャンル検索を Razor ページに追加します。

*Index.cshtml* を次のように変更します。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [前へ:ページの更新](xref:tutorials/razor-pages/da1)
> [次: 新しいフィールドの追加](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

次のセクションでは、*ジャンル*または*名前*による映画検索が追加されます。

強調表示されている次のプロパティを *Pages/Movies/Index.cshtml.cs* に追加します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: ユーザーが検索テキスト ボックスに入力したテキストが含まれる。 `SearchString` には、[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 属性があります。 `[BindProperty]` により、プロパティと同じ名前に基づきフォーム値とクエリ文字列がバインドされます。 GET 要求でのバインドには `(SupportsGet = true)` が必要です。
* `Genres`: ジャンル一覧が含まれる。 `Genres` により、ユーザーは一覧からジャンルを選択できます。 `SelectList` には `using Microsoft.AspNetCore.Mvc.Rendering;` が必要です。
* `MovieGenre`: "Western (西部劇)" など、ユーザーが選択する特定のジャンルが含まれる。
* `Genres` と `MovieGenre` は、このチュートリアルで後述します。

[!INCLUDE[](~/includes/bind-get.md)]

索引ページの `OnGetAsync` メソッドを次のコードに変更します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリが作成されます。

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

このクエリはこの時点では定義される*だけ*で、データベースに対して**実行されていません**。

`SearchString` プロパティが null でも空でもない場合、検索文字列で絞り込むようにムービークエリが変更されます。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。 ラムダは、メソッド ベースの [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリで、[Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) メソッドや `Contains` (先のコードで使用されています) など、標準クエリ演算子メソッドの引数として使用されます。 LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。 クエリ実行は先送りされます。 つまり、その具体値が繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延ばされます。 詳細については、「[クエリ実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。

**注:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは C# コードではなく、データベースで実行されます。 クエリの大文字と小文字の区別は、データベースや照合順序に依存します。 SQL Server では、`Contains` は大文字/小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。 SQLite では、既定の照合順序で、大文字と小文字が区別されます。

ムービーページに移動し、`?searchString=Ghost` のようなクエリ文字列を URL に追加します (例: `https://localhost:5001/Movies?searchString=Ghost`)。 フィルターされたムービーが表示されます。

![インデックス ビュー](search/_static/ghost.png)

次のルート テンプレートが索引ページに追加される場合、検索文字列を URL セグメントとして渡すことができます (例: `https://localhost:5001/Movies/Ghost`)。

```cshtml
@page "{searchString?}"
```

先のルート制約では、クエリ文字列値の代わりに、ルート データ (URL セグメント) として題名を検索できます。  `"{searchString?}"` の `?` は、これが任意のルート パラメーターであることを意味します。

![ghost という単語が URL に追加された索引ビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービーリストが返されています。](search/_static/g2.png)

ASP.NET Core ランタイムでは[モデル バインド](xref:mvc/models/model-binding)を使用し、クエリ文字列 (`?searchString=Ghost`) またはルート データ (`https://localhost:5001/Movies/Ghost`) から `SearchString` プロパティの値が設定されます。 モデル バインドでは、大文字と小文字が区別されません。

ただし、URL を変更してムービーを検索することをユーザーに求めることはできません。 この手順では、ムービーを絞り込むための UI を追加します。 ルート制約 `"{searchString?}"` を追加した場合、それを削除します。

*Pages/Movies/Index.cshtml* ファイルを開き、次のコードで強調表示されている `<form>` マークアップを追加します。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` タグでは、次の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)が使用されます。

* [フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper) フォームが提出されると、フィルター文字列がクエリ文字列経由で*ページ/ムービー/索引*ページに送信されます。
* [入力タグ ヘルパー](xref:mvc/views/working-with-forms#the-input-tag-helper)

変更を保存し、フィルターをテストします。

![タイトル フィルター テキストボックスに ghost という単語が入力されたインデックス ビュー](search/_static/filter.png)

## <a name="search-by-genre"></a>ジャンルで検索する

`OnGetAsync` メソッドを次のコードで更新します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

次のコードは、データベースからすべてのジャンルを取得する LINQ クエリです。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

ジャンルの `SelectList` は、別個のジャンルを推定することで作成されます。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>ジャンル検索を Razor ページに追加します。

*Index.cshtml* を次のように変更します。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。
前のコードでは、[選択タグ ヘルパー](xref:mvc/views/working-with-forms#the-select-tag-helper)とオプション タグ ヘルパーが使用されています。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [前へ:ページの更新](xref:tutorials/razor-pages/da1)
> [次: 新しいフィールドの追加](xref:tutorials/razor-pages/new-field)

::: moniker-end
