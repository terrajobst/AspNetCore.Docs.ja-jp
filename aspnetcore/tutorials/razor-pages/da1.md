---
title: ASP.NET Core アプリで生成済みページを更新する
author: rick-anderson
description: ASP.NET Core アプリで生成済みページを更新する方法について説明します。
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: f1f69b7facf584d46248405c808e75bdd8448d2b
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440324"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>ASP.NET Core アプリで生成済みページを更新する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

スキャフォールディングされたムービー アプリは上々の滑り出しでしたが、表示が理想的ではありません。 **ReleaseDate** は **Release Date** (2 つの単語) にする必要があります。

![Chrome で開かれているムービー アプリケーション](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>生成されたコードの更新

*Models/Movie.cs* ファイルを開き、下のコードで強調表示されている行を追加します。

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]` データ注釈により、Entity Framework Core でデータベースの通貨と `Price` が正しくマッピングできるようになります。 詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) については、次のチュートリアルで説明します。 [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) 属性は、フィールドの名前として表示する内容 (ここでは、"ReleaseDate" ではなく、"Release Date") を指定します。 [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性はデータ型 (Date) を指定するため、フィールドに格納される時刻情報は表示されません。

Pages/Movies を参照し、 **[編集]** リンクをポイントしてターゲット URL を確認します。

![[編集] リンクがマウスでポイントされ、リンク URL として http://localhost:1234/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](~/tutorials/razor-pages/da1/edit7.png)

**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクは、*Pages/Movies/Index.cshtml* ファイルで[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。 上のコードでは、`AnchorTagHelper` は動的に Razor ページからの HTML `href` 属性値 (ルートは相対)、`asp-page`、およびルート ID (`asp-route-id`) を生成します。 詳細については、「[ページの URL の生成](xref:razor-pages/index#url-generation-for-pages)」を参照してください。

お好みのブラウザーから **[ソースの表示]** を使用して、生成されたマークアップを確認します。 生成された HTML の部分を以下に示します。

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

動的に生成されたリンクは、クエリ文字列を含むムービー ID を渡します (例: `https://localhost:5001/Movies/Details?id=1` の `?id=1`)。

"{id:int}" ルート テンプレートを使用するには、[編集]、[詳細]、および [削除] Razor ページを更新します。 これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。 アプリを実行してから、ソースを表示します。 生成される HTML では、次のように URL のパス部分に ID を追加します。

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

整数を**含まない**、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。 たとえば、`http://localhost:5000/Movies/Details` の場合は 404 エラーが返されます。 ID を省略するには、次のように `?` をルート制約に追加します。

 ```cshtml
@page "{id:int?}"
```

`@page "{id:int?}"` の動作をテストするには

* *Pages/Movies/Details.cshtml* の page ディレクティブを `@page "{id:int?}"` に設定します。
* `public async Task<IActionResult> OnGetAsync(int? id)` (*Pages/Movies/Details.cshtml.cs* で) にブレークポイントを設定します
* `https://localhost:5001/Movies/Details/` に移動します。

`@page "{id:int}"` ディレクティブでは、ブレークポイントがヒットすることはありません。 ルーティング エンジンは、HTTP 404 を返します。 `@page "{id:int?}"` を使用すると、`OnGetAsync` メソッドは `NotFound` (HTTP 404) を返します。

### <a name="review-concurrency-exception-handling"></a>コンカレンシーの例外処理の確認

*Pages/Movies/Edit.cshtml.cs* ファイルで `OnPostAsync` メソッドを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

上のコードでは、一方のクライアントがムービーを削除し、もう一方のクライアントがムービーに変更を投稿した場合に、コンカレンシーの例外を検出します。

`catch` ブロックをテストするには、次の操作を行います。

* `catch (DbUpdateConcurrencyException)` へのブレークポイントの設定
* ムービーの **[編集]** を選択し、変更を行います。ただし、 **[保存]** はしないでください。
* 別のブラウザー ウィンドウで、同じムービーの **[削除]** リンクを選択してから、ムービーを削除します。
* 前のブラウザー ウィンドウで、ムービーに変更を投稿します。

実稼働環境のコードが、コンカレンシーの競合を検出する可能性があります。 詳細については、[コンカレンシーの競合の処理](xref:data/ef-rp/concurrency)に関するページを参照してください。

### <a name="posting-and-binding-review"></a>レビューの投稿とバインディング

*Pages/Movies/Edit.cshtml.cs* ファイルを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

HTTP GET 要求が Movies/Edit ページに対して行われた場合 (例: `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` メソッドはデータベースからムービーをフェッチし、`Page` メソッドを返します。
* `Page` メソッドは *Pages/Movies/Edit.cshtml* Razor ページをレンダリングします。 *Pages/Movies/Edit.cshtml* ファイルにはモデルのディレクティブ (`@model RazorPagesMovie.Pages.Movies.EditModel`) が含まれています。これにより、ページでムービー モデルが使用可能になります。
* [編集] フォームには、ムービーからの値が表示されます。

Movies/Edit ページが投稿された場合:

* ページのフォーム値は `Movie` プロパティにバインドされます。 `[BindProperty]` 属性により、[モデル バインド](xref:mvc/models/model-binding)が有効になります。

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* モデルの状態にエラーがある (たとえば、`ReleaseDate` を日付に変換できない) 場合、送信された値を含むフォームが再表示されます。
* モデル エラーがない場合、ムービーは保存されます。

[インデックス]、[作成]、および [削除] Razor ページの HTTP GET メソッドも同様のパターンに従います。 [作成] Razor ページの HTTP POST `OnPostAsync` メソッドも [編集] Razor ページの `OnPostAsync` メソッドと同様のパターンに従います。

## <a name="additional-resources"></a>その他の技術情報

> [!div class="step-by-step"]
> [前へ:データベースの操作](xref:tutorials/razor-pages/sql)
> [次: 検索の追加](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

スキャフォールディングされたムービー アプリは上々の滑り出しでしたが、表示が理想的ではありません。 **ReleaseDate** は **Release Date** (2 つの単語) にする必要があります。

![Chrome で開かれているムービー アプリケーション](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>生成されたコードの更新

*Models/Movie.cs* ファイルを開き、下のコードで強調表示されている行を追加します。

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]` データ注釈により、Entity Framework Core でデータベースの通貨と `Price` が正しくマッピングできるようになります。 詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) については、次のチュートリアルで説明します。 [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) 属性は、フィールドの名前として表示する内容 (ここでは、"ReleaseDate" ではなく、"Release Date") を指定します。 [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性はデータ型 (Date) を指定するため、フィールドに格納される時刻情報は表示されません。

Pages/Movies を参照し、 **[編集]** リンクをポイントしてターゲット URL を確認します。

![[編集] リンクがマウスでポイントされ、リンク URL として http://localhost:1234/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](~/tutorials/razor-pages/da1/edit7.png)

**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクは、*Pages/Movies/Index.cshtml* ファイルで[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。 上のコードでは、`AnchorTagHelper` は動的に Razor ページからの HTML `href` 属性値 (ルートは相対)、`asp-page`、およびルート ID (`asp-route-id`) を生成します。 詳細については、「[ページの URL の生成](xref:razor-pages/index#url-generation-for-pages)」を参照してください。

お好みのブラウザーから **[ソースの表示]** を使用して、生成されたマークアップを確認します。 生成された HTML の部分を以下に示します。

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

動的に生成されたリンクは、クエリ文字列を含むムービー ID を渡します (例: `https://localhost:5001/Movies/Details?id=1` の `?id=1`)。

"{id:int}" ルート テンプレートを使用するには、[編集]、[詳細]、および [削除] Razor ページを更新します。 これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。 アプリを実行してから、ソースを表示します。 生成される HTML では、次のように URL のパス部分に ID を追加します。

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

整数を**含まない**、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。 たとえば、`http://localhost:5000/Movies/Details` の場合は 404 エラーが返されます。 ID を省略するには、次のように `?` をルート制約に追加します。

 ```cshtml
@page "{id:int?}"
```

`@page "{id:int?}"` の動作をテストするには

* *Pages/Movies/Details.cshtml* の page ディレクティブを `@page "{id:int?}"` に設定します。
* `public async Task<IActionResult> OnGetAsync(int? id)` (*Pages/Movies/Details.cshtml.cs* で) にブレークポイントを設定します
* `https://localhost:5001/Movies/Details/` に移動します。

`@page "{id:int}"` ディレクティブでは、ブレークポイントがヒットすることはありません。 ルーティング エンジンは、HTTP 404 を返します。 `@page "{id:int?}"` を使用すると、`OnGetAsync` メソッドは `NotFound` (HTTP 404) を返します。

### <a name="review-concurrency-exception-handling"></a>コンカレンシーの例外処理の確認

*Pages/Movies/Edit.cshtml.cs* ファイルで `OnPostAsync` メソッドを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

上のコードでは、一方のクライアントがムービーを削除し、もう一方のクライアントがムービーに変更を投稿した場合に、コンカレンシーの例外を検出します。

`catch` ブロックをテストするには、次の操作を行います。

* `catch (DbUpdateConcurrencyException)` へのブレークポイントの設定
* ムービーの **[編集]** を選択し、変更を行います。ただし、 **[保存]** はしないでください。
* 別のブラウザー ウィンドウで、同じムービーの **[削除]** リンクを選択してから、ムービーを削除します。
* 前のブラウザー ウィンドウで、ムービーに変更を投稿します。

実稼働環境のコードが、コンカレンシーの競合を検出する可能性があります。 詳細については、[コンカレンシーの競合の処理](xref:data/ef-rp/concurrency)に関するページを参照してください。

### <a name="posting-and-binding-review"></a>レビューの投稿とバインディング

*Pages/Movies/Edit.cshtml.cs* ファイルを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

HTTP GET 要求が Movies/Edit ページに対して行われた場合 (例: `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` メソッドはデータベースからムービーをフェッチし、`Page` メソッドを返します。 
* `Page` メソッドは *Pages/Movies/Edit.cshtml* Razor ページをレンダリングします。 *Pages/Movies/Edit.cshtml* ファイルにはモデルのディレクティブ (`@model RazorPagesMovie.Pages.Movies.EditModel`) が含まれています。これにより、ページでムービー モデルが使用可能になります。
* [編集] フォームには、ムービーからの値が表示されます。

Movies/Edit ページが投稿された場合:

* ページのフォーム値は `Movie` プロパティにバインドされます。 `[BindProperty]` 属性により、[モデル バインド](xref:mvc/models/model-binding)が有効になります。

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* モデル状態にエラーがある (たとえば、`ReleaseDate` を日付に変換できない) 場合、フォームは送信された値で表示されます。
* モデル エラーがない場合、ムービーは保存されます。

[インデックス]、[作成]、および [削除] Razor ページの HTTP GET メソッドも同様のパターンに従います。 [作成] Razor ページの HTTP POST `OnPostAsync` メソッドも [編集] Razor ページの `OnPostAsync` メソッドと同様のパターンに従います。

次のチュートリアルでは検索を追加します。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [前へ:データベースの操作](xref:tutorials/razor-pages/sql)
> [次: 検索の追加](xref:tutorials/razor-pages/search)

::: moniker-end
