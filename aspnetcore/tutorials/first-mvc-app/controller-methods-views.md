---
title: ASP.NET Core のコントローラーのメソッドとビュー
author: rick-anderson
description: ASP.NET Core でコントローラー メソッド、ビュー、DataAnnotations を使用する方法について説明します。
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 6d960da5acecbb95893bd339b4391560bedffb3d
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815417"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a>ASP.NET Core のコントローラーのメソッドとビュー

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ムービー アプリは上々の滑り出しでしたが、表示が理想的ではありません。たとえば、**ReleaseDate** は 2 語でなければなりません。

![インデックス ビュー:Release Date が 1 語 (スペースなし) で、ムービーの公開日がすべて午前 12 時になっています](working-with-sql/_static/m55.png)

*Models/Movie.cs* ファイルを開き、下の画像で強調表示されている行を追加します。

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) については、次のチュートリアルで説明します。 [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) 属性は、フィールドの名前として表示する内容 (ここでは、"ReleaseDate" ではなく、"Release Date") を指定します。 [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性はデータ型 (Date) を指定するため、フィールドに格納される時刻情報は表示されません。

`[Column(TypeName = "decimal(18, 2)")]` データ注釈は、Entity Framework Core がデータベースの通貨と `Price` を正しくマッピングできるようにするために必要です。 詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。

`Movies` コントローラーを表示し、 **[編集]** リンクをマウスでポイントしてターゲットの URL を確認します。

![[編集] リンクがマウスでポイントされ、リンク URL として https://localhost:5001/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

**[編集]** 、 **[詳細]** 、 **[削除]** の各リンクは、*Views/Movies/Index.cshtml* ファイルで Core MVC アンカー タグ ヘルパーによって生成されます。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。 上のコードでは、`AnchorTagHelper` はコントローラーのアクション メソッドとルート ID から HTML の `href` 属性の値を動的に生成します。好みのブラウザーの **[ソースの表示]** または開発者ツールを使って、生成されたマークアップを確認してください。 生成された HTML の部分を以下に示します。

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

*Startup.cs* ファイルで設定する[ルーティング](xref:mvc/controllers/routing)の形式を思い出してください。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core は、`https://localhost:5001/Movies/Edit/4` を、`Movies` コントローラーの `Edit` アクション メソッドへの要求に変換し、パラメーター `Id` には 4 を設定します (コントローラー メソッドはアクション メソッドとも呼ばれます)。

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は、ASP.NET Core で最もよく使われる新機能の 1 つです。 詳細については、「[その他の技術情報](#additional-resources)」を参照してください。

`Movies` コントローラーを開き、2 つの `Edit` アクション メソッドを調べます。 次に示すコードの `HTTP GET Edit` メソッドは、ムービーをフェッチし、*Edit.cshtml* Razor ファイルによって生成される編集フォームを設定します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

次に示すコードの `HTTP POST Edit` メソッドは、送信されたムービーの値を処理します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

次に示すコードの `HTTP POST Edit` メソッドは、送信されたムービーの値を処理します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[Bind]` 属性は、[オーバーポスティング攻撃](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost)を防ぐための 1 つの方法です。 変更する `[Bind]` 属性にだけプロパティを含める必要があります。 詳細については、[オーバーポスティング攻撃からのコントローラーの保護](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)に関する記事を参照してください。 [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) は、オーバーポスティング攻撃を防ぐもう 1 つの方法を提供します。

2 番目の `Edit` アクション メソッドの前に `[HttpPost]` 属性が付いていることに注意してください。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

`HttpPost` 属性は、`POST` 要求に対して "*のみ*" この `Edit` メソッドを呼び出すことができることを指定します。 1 番目の Edit メソッドにも `[HttpGet]` 属性を適用してもかまいませんが、`[HttpGet]` が既定値なので必要ありません。

`ValidateAntiForgeryToken` 属性は[リクエスト フォージェリを防ぐ](xref:security/anti-request-forgery)ために使われ、編集ビュー ファイル (*Views/Movies/Edit.cshtml*) で生成されるフォージェリ対策トークンとペアにされます。 編集ビュー ファイルは、[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)でフォージェリ対策トークンを生成します。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)で生成される非表示のフォージェリ対策トークンは、Movies コントローラーの `Edit` メソッドで `[ValidateAntiForgeryToken]` によって生成されるフォージェリ対策トークンと一致している必要があります。 詳しくは、[リクエスト フォージェリの対策に関する記事](xref:security/anti-request-forgery)をご覧ください。

`HttpGet Edit` メソッドは、ムービーの `ID` パラメーターを受け取り、Entity Framework の `FindAsync` メソッドを使ってムービーを検索して、選択されたムービーを編集ビューに返します。 ムービーが見つからない場合は、`NotFound` (HTTP 404) が返されます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

スキャフォールディング システムが編集ビューを作成したときは、そのシステムが `Movie` クラスを調べて、クラスの各プロパティの `<label>` および `<input>` 要素をレンダリングするコードを作成しました。 次の例では、Visual Studio のスキャフォールディング システムによって生成された編集ビューを示します。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

ビュー テンプレートではファイルの先頭に `@model MvcMovie.Models.Movie` ステートメントがあることに注意してください。 `@model MvcMovie.Models.Movie` は、ビューがビュー テンプレートのモデルとして `Movie` 型を期待することを指定します。

スキャフォールディングされたコードは、複数のタグ ヘルパー メソッドを使って HTML マークアップを効率化します。 [ラベル タグ ヘルパー](xref:mvc/views/working-with-forms)は、フィールドの名前を表示します ("Title"、"ReleaseDate"、"Genre"、"Price")。 [入力タグ ヘルパー](xref:mvc/views/working-with-forms)は、HTML の `<input>` 要素をレンダリングします。 [検証タグ ヘルパー](xref:mvc/views/working-with-forms)は、そのプロパティに関連付けられている検証メッセージを表示します。

アプリケーションを実行し、`/Movies` URL に移動します。 **[編集]** リンクをクリックします。 ブラウザーで、ページのソースを表示します。 `<form>` 要素に対して生成された HTML を次に示します。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

`<input>` 要素は、`/Movies/Edit/id` URL に送信するように `action` 属性が設定された `HTML <form>` 要素に含まれます。 [`Save`] ボタンがクリックされると、フォームのデータがサーバーに送信されます。 `</form>` 要素を閉じる前に最後の行では、[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)によって生成された非表示の [XSRF](xref:security/anti-request-forgery) トークンが示されています。

## <a name="processing-the-post-request"></a>POST 要求の処理

次のリストでは、`Edit` アクション メソッドの `[HttpPost]` バージョンを示します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[ValidateAntiForgeryToken]` 属性は、[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)のフォージェリ対策トークン ジェネレーターによって生成された非表示の [XSRF](xref:security/anti-request-forgery) トークンを検証します。

[モデル バインド](xref:mvc/models/model-binding) システムは、送信されたフォーム値を取得し、`movie` パラメーターとして渡される `Movie` オブジェクトを作成します。 `ModelState.IsValid` メソッドは、フォームで送信されたデータを使って `Movie` オブジェクトを変更 (編集または更新) できることを検証します。 データが有効な場合は保存されます。 更新 (編集) されたムービー データは、データベース コンテキストの `SaveChangesAsync` メソッドを呼び出すことによってデータベースに保存されます。 データを保存した後、コードはユーザーを `MoviesController` クラスの `Index` アクション メソッドにリダイレクトします。そこでは、行われたばかりの変更を含むムービー コレクションが表示されます。

フォームがサーバーに送信される前に、クライアント側の検証はフィールドに対する検証規則を確認します。 検証エラーがある場合は、エラー メッセージが表示され、フォームは送信されません。 JavaScript が無効になっている場合、クライアント側検証は行われませんが、サーバーは送信された無効な値を検出し、フォーム値がエラー メッセージと共に再表示されます。 このチュートリアルで後ほど、[モデルの検証](xref:mvc/models/validation)についてさらに詳しく説明します。 *Views/Movies/Edit.cshtml* ビュー テンプレートの[検証タグ ヘルパー](xref:mvc/views/working-with-forms)は、適切なエラー メッセージの表示を処理します。

![編集ビュー:正しくない価格の値 abc に対する例外では、"Price フィールドは数値でなければなりません" と表示されます。 正しくないリリース日の値 xyz に対する例外では、"Please enter a valid date" と表示されます。](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Movie コントローラーのすべての `HttpGet` メソッドは、同様のパターンに従います。 ムービー オブジェクト (または、`Index` の場合はオブジェクトの一覧) を取得し、オブジェクト (モデル) をビューに渡します。 `Create` メソッドは、空のムービー オブジェクトを `Create` ビューに渡します。 データの作成、編集、削除、またはそれ以外の変更を行うすべてのメソッドは、メソッドの `[HttpPost]` のオーバーロードでそれを行います。 `HTTP GET` メソッドでデータを変更すると、セキュリティ リスクがあります。 `HTTP GET` メソッドでデータを変更することは、HTTP のベスト プラクティスや、GET 要求ではアプリケーションの状態を変更してはならないというアーキテクチャの [REST](http://rest.elkstein.org/) パターンにも、違反しています。 つまり、GET 操作の実行は、副作用がなく、永続化されたデータを変更しない、安全な操作である必要があります。

## <a name="additional-resources"></a>その他の技術情報

* [グローバライズとローカライズ](xref:fundamentals/localization)
* [Tag Helpers の概要](xref:mvc/views/tag-helpers/intro)
* [タグ ヘルパーの作成](xref:mvc/views/tag-helpers/authoring)
* [リクエスト フォージェリの対策](xref:security/anti-request-forgery)
* [オーバーポスティング攻撃](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)からのコントローラーの保護
* [ViewModel](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [フォーム タグ ヘルパー](xref:mvc/views/working-with-forms)
* [入力タグ ヘルパー](xref:mvc/views/working-with-forms)
* [ラベル タグ ヘルパー](xref:mvc/views/working-with-forms)
* [選択タグ ヘルパー](xref:mvc/views/working-with-forms)
* [検証タグ ヘルパー](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [前へ](working-with-sql.md)
> [次へ](search.md)  
