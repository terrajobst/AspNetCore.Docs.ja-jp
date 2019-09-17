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
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="15139-103">ASP.NET Core MVC アプリへの新しいフィールドの追加</span><span class="sxs-lookup"><span data-stu-id="15139-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="15139-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="15139-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="15139-105">このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。</span><span class="sxs-lookup"><span data-stu-id="15139-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="15139-106">モデルに新しいフィールドを追加する。</span><span class="sxs-lookup"><span data-stu-id="15139-106">Add a new field to the model.</span></span>
* <span data-ttu-id="15139-107">新しいフィールドをデータベースに移行する。</span><span class="sxs-lookup"><span data-stu-id="15139-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="15139-108">EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="15139-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="15139-109">テーブルをデータベースに追加して、データベースのスキーマを追跡します。</span><span class="sxs-lookup"><span data-stu-id="15139-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="15139-110">データベースが生成されたモデル クラスと同期されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="15139-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="15139-111">同期していない場合、EF は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="15139-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="15139-112">一貫性のないデータベース/コードの問題を簡単に見つけられます。</span><span class="sxs-lookup"><span data-stu-id="15139-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="15139-113">ムービー モデルへの評価プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="15139-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="15139-114">`Rating` プロパティを *Models/Movie.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="15139-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="15139-115">アプリをビルドする</span><span class="sxs-lookup"><span data-stu-id="15139-115">Build the app</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="15139-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15139-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="15139-117">Ctrl + Shift + B</span><span class="sxs-lookup"><span data-stu-id="15139-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="15139-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="15139-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

`dotnet build`

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="15139-119">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="15139-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="15139-120">コマンド ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="15139-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="15139-121">新しいフィールドを `Movie` クラスに追加したので、この新しいプロパティが含まれるように、拘束力のあるホワイト リストを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15139-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="15139-122">*MoviesController.cs* で、アクション メソッドの `Create` と `Edit` の両方の `[Bind]` 属性を更新し、`Rating` プロパティが含まれるようにします。</span><span class="sxs-lookup"><span data-stu-id="15139-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="15139-123">ブラウザー ビューで新しい `Rating` プロパティを表示、作成、編集するためにビュー テンプレートを更新します。</span><span class="sxs-lookup"><span data-stu-id="15139-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="15139-124">*/Views/Movies/Index.cshtml* ファイルを編集し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="15139-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="15139-125">*/Views/Movies/Create.cshtml* を `Rating` フィールドで更新します。</span><span class="sxs-lookup"><span data-stu-id="15139-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[<span data-ttu-id="15139-126">Visual Studio / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="15139-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="15139-127">前の "form group" をコピー/貼り付けし、intelliSense にフィールドを更新させることができます。</span><span class="sxs-lookup"><span data-stu-id="15139-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="15139-128">IntelliSense は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)と連動します。</span><span class="sxs-lookup"><span data-stu-id="15139-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![開発者は、ビューの 2 番目のラベル要素で、asp-for の属性値に文字 R を入力しました。](new-field/_static/cr.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="15139-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="15139-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="15139-133">残りのテンプレートを更新します。</span><span class="sxs-lookup"><span data-stu-id="15139-133">Update the remaining templates.</span></span>

<span data-ttu-id="15139-134">新しい列に値を提供するように、`SeedData` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="15139-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="15139-135">下に変更のサンプルがありますが、`new Movie` ごとにこの変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="15139-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="15139-136">DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。</span><span class="sxs-lookup"><span data-stu-id="15139-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="15139-137">ここで実行すると、次の `SqlException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="15139-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="15139-138">このエラーは、更新された Movie モデル クラスが既存のデータベースの Movie テーブルのスキーマと異なるために発生します。</span><span class="sxs-lookup"><span data-stu-id="15139-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="15139-139">(データベース テーブルに `Rating` 列はありません)。</span><span class="sxs-lookup"><span data-stu-id="15139-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="15139-140">このエラーを解決するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="15139-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="15139-141">Entity Framework に、新しいモデル クラス スキーマに基づいてデータベースを自動的にドロップさせ、再作成させます。</span><span class="sxs-lookup"><span data-stu-id="15139-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="15139-142">この手法は、開発周期の早い段階で、テスト データベースで開発しているときに非常に便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。</span><span class="sxs-lookup"><span data-stu-id="15139-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="15139-143">ただし、欠点もあり、データベースの既存データが失われます。本稼働データベースではこの手法は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="15139-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="15139-144">初期化子を利用し、データベースにテスト データを自動的に初期投入します。多くの場合、アプリケーション開発の手法として有益な方法です。</span><span class="sxs-lookup"><span data-stu-id="15139-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="15139-145">これは、初期の開発や SQLite を使用するときに適した方法です。</span><span class="sxs-lookup"><span data-stu-id="15139-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="15139-146">モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。</span><span class="sxs-lookup"><span data-stu-id="15139-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="15139-147">この手法の長所は、データが維持されることです。</span><span class="sxs-lookup"><span data-stu-id="15139-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="15139-148">この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="15139-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="15139-149">Code First Migrations を使用して、データベース スキーマを更新します。</span><span class="sxs-lookup"><span data-stu-id="15139-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="15139-150">このチュートリアルでは、Code First Migrations を使用します。</span><span class="sxs-lookup"><span data-stu-id="15139-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="15139-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15139-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="15139-152">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="15139-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC メニュー](adding-model/_static/pmc.png)

<span data-ttu-id="15139-154">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="15139-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="15139-155">`Add-Migration` コマンドは移行フレームワークに現在の `Movie` モデルを現在の `Movie` DB スキーマで調べ、DB を新しいモデルに移行するために必要なコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="15139-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="15139-156">"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。</span><span class="sxs-lookup"><span data-stu-id="15139-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="15139-157">移行ファイルには意味のある名前を使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="15139-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="15139-158">DB 内のレコードをすべて削除すると、初期化メソッドで DB がシードされ、`Rating` フィールドが追加されします。</span><span class="sxs-lookup"><span data-stu-id="15139-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="15139-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="15139-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="15139-160">データベースを削除し、移行を使ってデータベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="15139-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="15139-161">データベースを削除するには、データベース ファイル (*MvcMovie.db*) を削除します。</span><span class="sxs-lookup"><span data-stu-id="15139-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="15139-162">次に、`ef database update` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="15139-162">Then run the `ef database update` command:</span></span>

```console
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="15139-163">アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="15139-163">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="15139-164">`Rating` フィールドはビュー テンプレートの `Edit`、`Details`、`Delete` に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15139-164">You should add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="15139-165">[前へ](search.md)
> [次へ](validation.md)</span><span class="sxs-lookup"><span data-stu-id="15139-165">[Previous](search.md)
[Next](validation.md)</span></span>
