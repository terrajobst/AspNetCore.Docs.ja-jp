---
title: ASP.NET Core MVC アプリへの新しいフィールドの追加
author: rick-anderson
description: Entity Framework Code First Migrations を利用し、新しいフィールドをモデルに追加し、その変更をデータベースに移行します。
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 538af8469cf28b145d466a869ca3ca689d637ac6
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773914"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC アプリへの新しいフィールドの追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。

* モデルに新しいフィールドを追加する。
* 新しいフィールドをデータベースに移行する。

EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。

* テーブルをデータベースに追加して、データベースのスキーマを追跡します。
* データベースが生成されたモデル クラスと同期されていることを確認します。 同期していない場合、EF は例外をスローします。 一貫性のないデータベース/コードの問題を簡単に見つけられます。

## <a name="add-a-rating-property-to-the-movie-model"></a>ムービー モデルへの評価プロパティの追加

`Rating` プロパティを *Models/Movie.cs* に追加します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

アプリをビルドする

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl + Shift + B

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`dotnet build`

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

コマンド ⌘ + B

------

新しいフィールドを `Movie` クラスに追加したので、この新しいプロパティが含まれるように、拘束力のあるホワイト リストを更新する必要があります。 *MoviesController.cs* で、アクション メソッドの `Create` と `Edit` の両方の `[Bind]` 属性を更新し、`Rating` プロパティが含まれるようにします。

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

ブラウザー ビューで新しい `Rating` プロパティを表示、作成、編集するためにビュー テンプレートを更新します。

*/Views/Movies/Index.cshtml* ファイルを編集し、`Rating` フィールドを追加します。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

*/Views/Movies/Create.cshtml* を `Rating` フィールドで更新します。

# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[Visual Studio / Visual Studio for Mac](#tab/visual-studio+visual-studio-mac)

前の "form group" をコピー/貼り付けし、intelliSense にフィールドを更新させることができます。 IntelliSense は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)と連動します。

![開発者は、ビューの 2 番目のラベル要素で、asp-for の属性値に文字 R を入力しました。 Intellisense のコンテキスト メニューが表示され、[評価] を含む、利用可能なフィールドが表示されました。[評価] は一覧の中で自動的に強調表示されています。 開発者はこのフィールドをクリックするか、キーボードの Enter を押すと、値が [評価] に設定されます。](new-field/_static/cr.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

残りのテンプレートを更新します。

新しい列に値を提供するように、`SeedData` クラスを更新します。 下に変更のサンプルがありますが、`new Movie` ごとにこの変更を行ってください。

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。 ここで実行すると、次の `SqlException` がスローされます。

`SqlException: Invalid column name 'Rating'.`

このエラーは、更新された Movie モデル クラスが既存のデータベースの Movie テーブルのスキーマと異なるために発生します。 (データベース テーブルに `Rating` 列はありません)。

このエラーを解決するための手法がいくつかあります。

1. Entity Framework に、新しいモデル クラス スキーマに基づいてデータベースを自動的にドロップさせ、再作成させます。 この手法は、開発周期の早い段階で、テスト データベースで開発しているときに非常に便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。 ただし、欠点もあり、データベースの既存データが失われます。本稼働データベースではこの手法は推奨されません。 初期化子を利用し、データベースにテスト データを自動的に初期投入します。多くの場合、アプリケーション開発の手法として有益な方法です。 これは、初期の開発や SQLite を使用するときに適した方法です。

2. モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。 この手法の長所は、データが維持されることです。 この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。

3. Code First Migrations を使用して、データベース スキーマを更新します。

このチュートリアルでは、Code First Migrations を使用します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。

  ![PMC メニュー](adding-model/_static/pmc.png)

PMC で、次のコマンドを入力します。

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` コマンドは移行フレームワークに現在の `Movie` モデルを現在の `Movie` DB スキーマで調べ、DB を新しいモデルに移行するために必要なコードを作成します。

"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。 移行ファイルには意味のある名前を使用すると便利です。

DB 内のレコードをすべて削除すると、初期化メソッドで DB がシードされ、`Rating` フィールドが追加されします。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

データベースを削除し、移行を使ってデータベースを再作成します。 データベースを削除するには、データベース ファイル (*MvcMovie.db*) を削除します。 次に、`ef database update` コマンドを実行します。

```console
dotnet ef database update
```

---
<!-- End of VS tabs -->

アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。 `Rating` フィールドはビュー テンプレートの `Edit`、`Details`、`Delete` に追加する必要があります。

> [!div class="step-by-step"]
> [前へ](search.md)
> [次へ](validation.md)
