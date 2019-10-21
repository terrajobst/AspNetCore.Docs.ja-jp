---
title: ASP.NET Core MVC アプリへのビューの追加
author: rick-anderson
description: 単純な ASP.NET Core MVC アプリにビューを追加する
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: de75c3b0651c0cda6629af786d7db9dc83bc4fef
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288828"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC アプリへのビューの追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

このセクションでは、[Razor](xref:mvc/views/razor) ビュー ファイルを使用して、クライアントへの HTML 応答を生成するプロセスを完全にカプセル化するために `HelloWorldController` クラスを変更します。

ビュー テンプレート ファイルは Razor を使用して作成します。 Razor ベースのビュー テンプレートには *.cshtml* ファイル拡張子が含まれています。 C# を使用して HTML 出力を作成する洗練された方法が提供されます。

現在、`Index` メソッドは、コントローラー クラスでハード コーディングされるメッセージを含む文字列を返します。 `HelloWorldController` クラスでは、`Index` メソッドを次のコードで置き換えます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

上のコードでは、コントローラーの <xref:Microsoft.AspNetCore.Mvc.Controller.View*> メソッドを呼び出します。 ビュー テンプレートを使用して、HTML 応答を生成します。 上記の `Index` メソッドなどのコントローラー メソッド (*アクション メソッド*ともいう) は、一般に、`string` などの型ではなく、<xref:Microsoft.AspNetCore.Mvc.IActionResult> (または <xref:Microsoft.AspNetCore.Mvc.ActionResult> から派生したクラス) を返します。

## <a name="add-a-view"></a>ビューを追加する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。

* *Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しい項目]** の順に選択します。

* **[新しい項目の追加 - MvcMovie]** ダイアログ

  * 右上の検索ボックスに「*view*」と入力します。

  * **[Razor ビュー]** を選択します。

  * **[名前]** ボックスの値、*Index.cshtml* を維持します。

  * **[追加]** を選択します。

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`HelloWorldController` の `Index` ビューを追加します。

* *Views/HelloWorld* という名前の新しいフォルダーを追加します。
* *Views/HelloWorld* フォルダー名 *Index.cshtml* に新しいファイルを追加します。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* *Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。
* *Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しいファイル]** の順に選択します。
* **[新しいファイル]** ダイアログで次を実行します。

  * 左側のウィンドウで **[Web]** を選択します。
  * 中央のウィンドウで **[空の HTML ファイル]** を選択します。
  * **[名前]** ボックスに「*Index.cshtml*」と入力します。
  * **[新規]** を選択します。

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view_mac.png)

---

*Views/HelloWorld/Index.cshtml* Razor ビュー ファイルの内容を次のコードに置き換えます。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

`https://localhost:{PORT}/HelloWorld` に移動します。 `HelloWorldController` の `Index` メソッドでは多くのことは行いませんでした。つまり、ステートメント `return View();` を実行し、メソッドでビュー テンプレート ファイルを使用して、ブラウザーへの応答をレンダリングするよう指定しただけです。 ビュー テンプレートのファイル名が指定されていないため、MVC では既定のビュー ファイルが使われました。 既定のビュー ファイルはメソッド (`Index`) と同じ名前なので、 */Views/HelloWorld/Index.cshtml* が使われます。 次のイメージは、ビューにハード コーディングされた "Hello from our View Template!" という文字列を示しています。

![ブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>ビューとレイアウト ページを変更する

メニューのリンク ( **[MvcMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。 各ページには同じメニューのレイアウトが表示されます。 メニューのレイアウトは、*Views/Shared/_Layout.cshtml* ファイルに実装されています。 *Views/Shared/_Layout.cshtml* ファイルを開きます。

[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。 `@RenderBody()` という行を見つけます。 `RenderBody` は、作成したビュー固有のページがすべて表示されるプレースホルダーで、レイアウト ページに*ラップ*されます。 たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Views/Home/Privacy.cshtml** ビューがレンダリングされます。

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>レイアウト ファイルでのタイトル、フッター、およびメニュー リンクの変更

*Views/Shared/_Layout.cshtml* ファイルの内容を次のマークアップに置き換えます。 変更が強調表示されています。

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

上記のマークアップでは、次の変更が加えられています。

* `MvcMovie` ではなく `Movie App` の発生回数が 3 回になりました。
* アンカー要素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` が `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` になりました。

上記のマークアップでは、このアプリで[領域](xref:mvc/controllers/areas)が使用されていないため、`asp-area=""` [アンカー タグ ヘルパー属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)と属性値は省略されました。

**注**:`Movies` コントローラーは実装されていません。 この時点で、`Movie App`リンクは機能しません。

ご自分の変更を保存し、**プライバシー** リンクを選択します。 ブラウザー タブのタイトルが、**Privacy Policy - Mvc Movie** ではなく、**Privacy Policy - Movie App** になっていることに注目してください。

![[プライバシー] タブ](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

**[ホーム]** リンクをタップし、タイトルとアンカー テキストにも **[Movie App]** と表示されていることを確認してください。 レイアウト テンプレートで一度変更しただけで、サイト上のすべてのページに新しいリンク テキストと新しいタイトルが反映できました。

*Views/_ViewStart.cshtml* ファイルを確認します。

```HTML
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* ファイルは *Views/Shared/_Layout.cshtml* ファイルに取り込まれ、各ビューに適用されます。 `Layout` プロパティを使用すれば、別のレイアウト ビューを設定することも、`null` に設定してレイアウト ファイルが使用されないようにすることもできます。

*Views/HelloWorld/Index.cshtml* ビュー ファイルのタイトルと `<h2>` 要素を次のように変更します。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

タイトルと `<h2>` 要素は若干異なります。これにより、コードのどの部分によって表示が変更されるのかを確認できます。

上のコードの `ViewData["Title"] = "Movie List";` では、`Title` ディクショナリの `ViewData` プロパティを "Movie List" に設定します。 `Title` プロパティは、次のように、レイアウト ページの `<title>` HTML 要素で使用されます。

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

変更内容を保存して、`https://localhost:{PORT}/HelloWorld` に移動します。 ブラウザーのタイトル、プライマリ見出し、およびセカンダリ見出しが変更されていることに注意してください (ブラウザーに変更内容が表示されない場合は、キャッシュされたコンテンツを表示している可能性があります。 ブラウザーで Ctrl + F5 キーを押して、サーバーからの応答が強制的に読み込まれるようにしてください)。ブラウザーのタイトルは、*Index.cshtml* ビュー テンプレートで設定した `ViewData["Title"]` で作成されます。レイアウト ファイルには "- Movie App" が追加されます。

*Index.cshtml* ビュー テンプレート内のコンテンツは、*Views/Shared/_Layout.cshtml* ビュー テンプレートにマージされます。 1 つの HTML 応答がブラウザーに送信されます。 レイアウト テンプレートを使用すれば、アプリのすべてのページに適用される変更を簡単に行うことができます。 詳細については、「[Layout](xref:mvc/views/layout)」(レイアウト) を参照してください。

![ムービー リスト ビュー](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

ここでは、"データ" のごく一部 (この場合は "Hello from our View Template!" というメッセージ) を ハード コーディングしました。 MVC アプリケーションには "V" (ビュー) があり、"C" (コントローラー) もありますが、"M" (モデル) はまだありません。

## <a name="passing-data-from-the-controller-to-the-view"></a>コントローラーからビューへのデータの受け渡し

コントローラー アクションは、受信 URL 要求への応答として呼び出されます。 コントローラー クラスでは、受信ブラウザー要求を処理するコードが記述されます。 コントローラーはデータ ソースからデータを取得し、ブラウザーに返す応答の種類を決定します。 ビュー テンプレートを使用すれば、コントローラーからブラウザーへの HTML 応答を生成して書式を設定できます。

コントローラーは、ビュー テンプレートで応答をレンダリングするために必要なデータを提供します。 ベスト プラクティス: ビュー テンプレートでは、ビジネス ロジックを実行したり、データベースと直接やりとりしたり**しない**でください。 ビュー テンプレートでは、コントローラーによって提供されるデータのみを処理するようにしてください。 この "懸念事項の分離" を維持すれば、コードをクリーンでテスト可能な保守しやすい状態に保つことが楽になります。

現時点では、`HelloWorldController` クラスの `Welcome` メソッドは `name` と `ID` パラメーターを受け取ってから、ブラウザーに直接値を出力します。 コントローラーにこの応答を文字列としてレンダリングさせるのではなく、代わりにビュー テンプレートを使用するようにコントローラーを変更します。 このビュー テンプレートでは動的応答が生成されます。これは、応答を生成するために、コントローラーからビューに適量のデータを渡す必要があることを意味します。 そのためには、コントローラーで動的データ (パラメーター) を設定します。これは、ビュー テンプレートでアクセスできるようにするために `ViewData` ディレクトリに必要なデータです。

*HelloWorldController.cs* 内で、`Welcome` メソッドを変更して `Message` および `NumTimes` 値を `ViewData` ディクショナリに追加します。 `ViewData` ディクショナリは動的オブジェクトです。つまり、任意の型を使用することができます。`ViewData` オブジェクトでは、その内部に何かを設定するまでプロパティは定義されません。 [MVC のモデル バインド システム](xref:mvc/models/model-binding)は、名前付きパラメーター (`name` と `numTimes`) を、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。 完全な *HelloWorldController.cs* ファイルは次のようになります。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` ディクショナリ オブジェクトには、ビューに渡されるデータが含まれています。

*Views/HelloWorld/Welcome.cshtml* という名前の [ようこそ] ビュー テンプレートを作成します。

"Hello" `NumTimes` を表示する *Welcome.cshtml* ビュー テンプレートでループを作成します。 *Views/HelloWorld/Welcome.cshtml* の内容を次のコードに置き換えます。

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

変更内容を保存し、次の URL を参照します。

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

データは URL から取得され、[MVC モデル バインダー](xref:mvc/models/model-binding)を使用してコントローラーに渡されます。 コントローラーはデータを `ViewData` ディクショナリにパッケージ化し、そのオブジェクトをビューに渡します。 その後、ビューでブラウザーに HTML としてデータがレンダリングされます。

![[ようこそ] ラベルと、Hello Rick という語句が 4 つ示された [プライバシー] ビュー](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

上のサンプルでは、`ViewData` ディクショナリを使用して、コントローラーからビューにデータを渡しました。 チュートリアルの後半では、ビュー モデルを使用して、コントローラーからビューにデータを渡します。 一般には、`ViewData` ディクショナリを使用する方法より、ビュー モデルを使用してデータを渡す方法が推奨されます。 詳細については、[ViewBag、ViewData、または TempData を使用するタイミング](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)に関するページをご覧ください。

次のチュートリアルでは、ムービーのデータベースを作成します。

> [!div class="step-by-step"]
> [前へ](adding-controller.md)
> [次へ](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このセクションでは、[Razor](xref:mvc/views/razor) ビュー ファイルを使用して、クライアントへの HTML 応答を生成するプロセスを完全にカプセル化するために `HelloWorldController` クラスを変更します。

ビュー テンプレート ファイルは Razor を使用して作成します。 Razor ベースのビュー テンプレートには *.cshtml* ファイル拡張子が含まれています。 C# を使用して HTML 出力を作成する洗練された方法が提供されます。

現在、`Index` メソッドは、コントローラー クラスでハード コーディングされるメッセージを含む文字列を返します。 `HelloWorldController` クラスでは、`Index` メソッドを次のコードで置き換えます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

上のコードでは、コントローラーの <xref:Microsoft.AspNetCore.Mvc.Controller.View*> メソッドを呼び出します。 ビュー テンプレートを使用して、HTML 応答を生成します。 上記の `Index` メソッドなどのコントローラー メソッド (*アクション メソッド*ともいう) は、一般に、`string` などの型ではなく、<xref:Microsoft.AspNetCore.Mvc.IActionResult> (または <xref:Microsoft.AspNetCore.Mvc.ActionResult> から派生したクラス) を返します。

## <a name="add-a-view"></a>ビューを追加する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。

* *Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しい項目]** の順に選択します。

* **[新しい項目の追加 - MvcMovie]** ダイアログ

  * 右上の検索ボックスに「*view*」と入力します。

  * **[Razor ビュー]** を選択します。

  * **[名前]** ボックスの値、*Index.cshtml* を維持します。

  * **[追加]** を選択します。

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`HelloWorldController` の `Index` ビューを追加します。

* *Views/HelloWorld* という名前の新しいフォルダーを追加します。
* *Views/HelloWorld* フォルダー名 *Index.cshtml* に新しいファイルを追加します。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* *Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。
* *Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しいファイル]** の順に選択します。
* **[新しいファイル]** ダイアログで次を実行します。

  * 左側のウィンドウで **[Web]** を選択します。
  * 中央のウィンドウで **[空の HTML ファイル]** を選択します。
  * **[名前]** ボックスに「*Index.cshtml*」と入力します。
  * **[新規]** を選択します。

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view_mac.png)

---

*Views/HelloWorld/Index.cshtml* Razor ビュー ファイルの内容を次のコードに置き換えます。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

`https://localhost:{PORT}/HelloWorld` に移動します。 `HelloWorldController` の `Index` メソッドでは多くのことは行いませんでした。つまり、ステートメント `return View();` を実行し、メソッドでビュー テンプレート ファイルを使用して、ブラウザーへの応答をレンダリングするよう指定しただけです。 ビュー テンプレートのファイル名が指定されていないため、MVC では既定のビュー ファイルが使われました。 既定のビュー ファイルはメソッド (`Index`) と同じ名前なので、 */Views/HelloWorld/Index.cshtml* が使われます。 次のイメージは、ビューにハード コーディングされた "Hello from our View Template!" という文字列を示しています。

![ブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>ビューとレイアウト ページを変更する

メニューのリンク ( **[MvcMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。 各ページには同じメニューのレイアウトが表示されます。 メニューのレイアウトは、*Views/Shared/_Layout.cshtml* ファイルに実装されています。 *Views/Shared/_Layout.cshtml* ファイルを開きます。

[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。 `@RenderBody()` という行を見つけます。 `RenderBody` は、作成したビュー固有のページがすべて表示されるプレースホルダーで、レイアウト ページに*ラップ*されます。 たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Views/Home/Privacy.cshtml** ビューがレンダリングされます。

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>レイアウト ファイルでのタイトル、フッター、およびメニュー リンクの変更

* タイトル要素とフッター要素で、`MvcMovie` を `Movie App` に変更します。
* アンカー要素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` を `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` に変更します。

次のマークアップには、強調表示された変更点が示されています。

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

上記のマークアップでは、このアプリで[領域](xref:mvc/controllers/areas)が使用されていないため、`asp-area` [アンカー タグ ヘルパー属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)は省略されました。

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**注**:`Movies` コントローラーは実装されていません。 この時点で、`Movie App`リンクは機能しません。

ご自分の変更を保存し、**プライバシー** リンクを選択します。 ブラウザー タブのタイトルが、**Privacy Policy - Mvc Movie** ではなく、**Privacy Policy - Movie App** になっていることに注目してください。

![[プライバシー] タブ](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

**[ホーム]** リンクをタップし、タイトルとアンカー テキストにも **[Movie App]** と表示されていることを確認してください。 レイアウト テンプレートで一度変更しただけで、サイト上のすべてのページに新しいリンク テキストと新しいタイトルが反映できました。

*Views/_ViewStart.cshtml* ファイルを確認します。

```HTML
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* ファイルは *Views/Shared/_Layout.cshtml* ファイルに取り込まれ、各ビューに適用されます。 `Layout` プロパティを使用すれば、別のレイアウト ビューを設定することも、`null` に設定してレイアウト ファイルが使用されないようにすることもできます。

*Views/HelloWorld/Index.cshtml* ビュー ファイルのタイトルと `<h2>` 要素を次のように変更します。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

タイトルと `<h2>` 要素は若干異なります。これにより、コードのどの部分によって表示が変更されるのかを確認できます。

上のコードの `ViewData["Title"] = "Movie List";` では、`Title` ディクショナリの `ViewData` プロパティを "Movie List" に設定します。 `Title` プロパティは、次のように、レイアウト ページの `<title>` HTML 要素で使用されます。

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

変更内容を保存して、`https://localhost:{PORT}/HelloWorld` に移動します。 ブラウザーのタイトル、プライマリ見出し、およびセカンダリ見出しが変更されていることに注意してください (ブラウザーに変更内容が表示されない場合は、キャッシュされたコンテンツを表示している可能性があります。 ブラウザーで Ctrl + F5 キーを押して、サーバーからの応答が強制的に読み込まれるようにしてください)。ブラウザーのタイトルは、*Index.cshtml* ビュー テンプレートで設定した `ViewData["Title"]` で作成されます。レイアウト ファイルには "- Movie App" が追加されます。

*Index.cshtml* ビュー テンプレートのコンテンツがどのように *Views/Shared/_Layout.cshtml* ビュー テンプレートにマージされ、1 つの HTML 応答がブラウザーに送信されたかにも注目してください。 レイアウト テンプレートを使用すれば、アプリケーションのすべてのページに適用される変更をとても簡単に行うことができます。 詳細については、「[Layout](xref:mvc/views/layout)」 (レイアウト) を参照してください。

![ムービー リスト ビュー](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

ここでは、"データ" のごく一部 (この場合は "Hello from our View Template!" というメッセージ) を ハード コーディングしました。 MVC アプリケーションには "V" (ビュー) があり、"C" (コントローラー) もありますが、"M" (モデル) はまだありません。

## <a name="passing-data-from-the-controller-to-the-view"></a>コントローラーからビューへのデータの受け渡し

コントローラー アクションは、受信 URL 要求への応答として呼び出されます。 コントローラー クラスでは、受信ブラウザー要求を処理するコードが記述されます。 コントローラーはデータ ソースからデータを取得し、ブラウザーに返す応答の種類を決定します。 ビュー テンプレートを使用すれば、コントローラーからブラウザーへの HTML 応答を生成して書式を設定できます。

コントローラーは、ビュー テンプレートで応答をレンダリングするために必要なデータを提供します。 ベスト プラクティス: ビュー テンプレートでは、ビジネス ロジックを実行したり、データベースと直接やりとりしたり**しない**でください。 ビュー テンプレートでは、コントローラーによって提供されるデータのみを処理するようにしてください。 この "懸念事項の分離" を維持すれば、コードをクリーンでテスト可能な保守しやすい状態に保つことが楽になります。

現時点では、`HelloWorldController` クラスの `Welcome` メソッドは `name` と `ID` パラメーターを受け取ってから、ブラウザーに直接値を出力します。 コントローラーにこの応答を文字列としてレンダリングさせるのではなく、代わりにビュー テンプレートを使用するようにコントローラーを変更します。 このビュー テンプレートでは動的応答が生成されます。これは、応答を生成するために、コントローラーからビューに適量のデータを渡す必要があることを意味します。 そのためには、コントローラーで動的データ (パラメーター) を設定します。これは、ビュー テンプレートでアクセスできるようにするために `ViewData` ディレクトリに必要なデータです。

*HelloWorldController.cs* 内で、`Welcome` メソッドを変更して `Message` および `NumTimes` 値を `ViewData` ディクショナリに追加します。 `ViewData` ディクショナリは動的オブジェクトです。つまり、任意の型を使用することができます。`ViewData` オブジェクトでは、その内部に何かを設定するまでプロパティは定義されません。 [MVC のモデル バインド システム](xref:mvc/models/model-binding)は、名前付きパラメーター (`name` と `numTimes`) を、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。 完全な *HelloWorldController.cs* ファイルは次のようになります。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` ディクショナリ オブジェクトには、ビューに渡されるデータが含まれています。

*Views/HelloWorld/Welcome.cshtml* という名前の [ようこそ] ビュー テンプレートを作成します。

"Hello" `NumTimes` を表示する *Welcome.cshtml* ビュー テンプレートでループを作成します。 *Views/HelloWorld/Welcome.cshtml* の内容を次のコードに置き換えます。

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

変更内容を保存し、次の URL を参照します。

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

データは URL から取得され、[MVC モデル バインダー](xref:mvc/models/model-binding)を使用してコントローラーに渡されます。 コントローラーはデータを `ViewData` ディクショナリにパッケージ化し、そのオブジェクトをビューに渡します。 その後、ビューでブラウザーに HTML としてデータがレンダリングされます。

![[ようこそ] ラベルと、Hello Rick という語句が 4 つ示された [プライバシー] ビュー](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

上のサンプルでは、`ViewData` ディクショナリを使用して、コントローラーからビューにデータを渡しました。 チュートリアルの後半では、ビュー モデルを使用して、コントローラーからビューにデータを渡します。 一般には、`ViewData` ディクショナリを使用する方法より、ビュー モデルを使用してデータを渡す方法が推奨されます。 詳細については、[ViewBag、ViewData、または TempData を使用するタイミング](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)に関するページをご覧ください。

次のチュートリアルでは、ムービーのデータベースを作成します。

> [!div class="step-by-step"]
> [前へ](adding-controller.md)
> [次へ](adding-model.md)

::: moniker-end
