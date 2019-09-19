---
title: ASP.NET Core の Razor ページと EF Core - 関連データの読み込み - 6/8
author: tdykstra
description: このチュートリアルでは、関連データ (Entity Framework がナビゲーション プロパティに読み込むデータ) の読み取りと表示を行います。
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: f53f8edef7fe8690d0e414bc094d81dc99ad198a
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082090"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="ca6f3-103">ASP.NET Core の Razor ページと EF Core - 関連データの読み込み - 6/8</span><span class="sxs-lookup"><span data-stu-id="ca6f3-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="ca6f3-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca6f3-105">このチュートリアルでは、関連データを読み取って表示する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="ca6f3-106">関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="ca6f3-107">以下の図は、このチュートリアルの完成したページを示しています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="ca6f3-110">一括読み込み、明示的読み込み、遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="ca6f3-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="ca6f3-111">EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="ca6f3-112">[一括読み込み](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="ca6f3-113">一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="ca6f3-114">エンティティが読み取られるときに、その関連データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="ca6f3-115">これは通常、必要なデータをすべて取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="ca6f3-116">EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="ca6f3-117">複数のクエリを発行することは、1 つの巨大なクエリよりも効率的である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="ca6f3-118">一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="ca6f3-120">一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="ca6f3-121">メイン クエリに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="ca6f3-121">One query for the main query</span></span> 
  * <span data-ttu-id="ca6f3-122">読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="ca6f3-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="ca6f3-123">`Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="ca6f3-124">"修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="ca6f3-125">`Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="ca6f3-127">メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="ca6f3-128">ナビゲーション プロパティのデータが明示的に含まれ*ない*場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="ca6f3-129">[明示的読み込み](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="ca6f3-130">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ca6f3-131">必要なときに関連するデータを取得するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="ca6f3-132">分離したクエリによる明示的読み込みにより、複数のクエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="ca6f3-133">明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="ca6f3-134">明示的読み込みを行うには、`Load` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="ca6f3-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-135">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="ca6f3-137">[遅延読み込み](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ca6f3-138">[遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ca6f3-139">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ca6f3-140">ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="ca6f3-141">初めてナビゲーション プロパティにアクセスされるたびに、クエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="ca6f3-142">Course ページの作成</span><span class="sxs-lookup"><span data-stu-id="ca6f3-142">Create Course pages</span></span>

<span data-ttu-id="ca6f3-143">`Course` エンティティには、関連する `Department` エンティティを含むナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="ca6f3-145">コースに割り当てられている部署の名前を表示するには</span><span class="sxs-lookup"><span data-stu-id="ca6f3-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="ca6f3-146">関連する `Department` エンティティを `Course.Department` ナビゲーション プロパティに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="ca6f3-147">`Department` エンティティの `Name` プロパティから名前を取得します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="ca6f3-148">Course ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="ca6f3-148">Scaffold Course pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca6f3-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca6f3-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ca6f3-150">次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="ca6f3-151">*Pages/Courses* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="ca6f3-152">モデル クラスに `Course` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="ca6f3-153">新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca6f3-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca6f3-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ca6f3-155">*Pages/Courses* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="ca6f3-156">次のコマンドを実行して、Course モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="ca6f3-157">**Windows の場合:**</span><span class="sxs-lookup"><span data-stu-id="ca6f3-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="ca6f3-158">**Linux または macOS の場合**:</span><span class="sxs-lookup"><span data-stu-id="ca6f3-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="ca6f3-159">*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="ca6f3-160">スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="ca6f3-161">`Include` メソッドが一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="ca6f3-162">アプリを実行し、 **[Courses]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="ca6f3-163">Department 列に `DepartmentID` が表示されますが、これには役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="ca6f3-164">部署名の表示</span><span class="sxs-lookup"><span data-stu-id="ca6f3-164">Display the department name</span></span>

<span data-ttu-id="ca6f3-165">次のコードを使用して Pages/Courses/Index.cshtml.cs を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="ca6f3-166">上記のコードでは、`Course` プロパティを `Courses` に変更し、`AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="ca6f3-167">`AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="ca6f3-168">エンティティは現在のコンテキストでは更新されないため、追跡する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="ca6f3-169">次のコードを使用して *Pages/Courses/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="ca6f3-170">スキャフォールディング コードに、次の変更が行われました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="ca6f3-171">`Course` プロパティ名は `Courses` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="ca6f3-172">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="ca6f3-173">既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="ca6f3-174">ただし、このケースでは、主キーは意味があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="ca6f3-175">部門名が表示されるように、**Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="ca6f3-176">コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="ca6f3-177">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="ca6f3-179">Select を使用した関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="ca6f3-179">Loading related data with Select</span></span>

<span data-ttu-id="ca6f3-180">`OnGetAsync` メソッドでは、`Include` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="ca6f3-181">`Select` メソッドは、必要な関連データだけを読み込む代替手段です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="ca6f3-182">`Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="ca6f3-183">コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="ca6f3-184">次のコードは、`Select` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="ca6f3-185">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="ca6f3-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="ca6f3-186">完全な例については、[IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-186">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="ca6f3-187">Instructor ページの作成</span><span class="sxs-lookup"><span data-stu-id="ca6f3-187">Create Instructor pages</span></span>

<span data-ttu-id="ca6f3-188">このセクションでは、Instructor ページをスキャフォールディングし、関連する Courses と Enrollments を Instructors Index ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="ca6f3-189">![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-189">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="ca6f3-190">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="ca6f3-191">インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="ca6f3-192">`Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ca6f3-193">`OfficeAssignment` エンティティには一括読み込みが使用されています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="ca6f3-194">一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="ca6f3-195">この場合、インストラクターへのオフィスの割り当てが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="ca6f3-196">ユーザーがインストラクターを選択すると、関連する `Course` エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="ca6f3-197">`Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="ca6f3-198">`Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="ca6f3-199">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="ca6f3-200">この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="ca6f3-201">ユーザーがコースを選択すると、`Enrollments` エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="ca6f3-202">上の図では、受講者名とグレードが表示されています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="ca6f3-203">`Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="ca6f3-204">ビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-204">Create a view model</span></span>

<span data-ttu-id="ca6f3-205">Instructors ページには、3 つの異なるテーブルからのデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="ca6f3-206">3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが必要です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="ca6f3-207">次のコードを使用して、*SchoolViewModels/InstructorIndexData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="ca6f3-208">Instructor ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="ca6f3-208">Scaffold Instructor pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca6f3-209">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca6f3-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ca6f3-210">次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="ca6f3-211">*Pages/Instructors* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="ca6f3-212">モデル クラスに `Instructor` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="ca6f3-213">新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca6f3-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca6f3-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ca6f3-215">*Pages/Instructors* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="ca6f3-216">次のコマンドを実行して、Instructor モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="ca6f3-217">**Windows の場合:**</span><span class="sxs-lookup"><span data-stu-id="ca6f3-217">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="ca6f3-218">**Linux または macOS の場合**:</span><span class="sxs-lookup"><span data-stu-id="ca6f3-218">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="ca6f3-219">更新する前にスキャフォールディング ページがどのように表示されるかを確認するには、アプリを実行し、Instructors ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="ca6f3-220">次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="ca6f3-221">`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="ca6f3-222">*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="ca6f3-223">このコードでは、次のナビゲーション プロパティに一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="ca6f3-224">`CourseAssignments` と `Course` に対する `Include` メソッドと `ThenInclude` メソッドの繰り返しに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="ca6f3-225">この繰り返しは、`Course` エンティティの 2 つのナビゲーション プロパティに一括読み込みを指定するために必要です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="ca6f3-226">次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="ca6f3-227">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="ca6f3-228">ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="ca6f3-229">`Where` メソッドはコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="ca6f3-230">しかし、この場合は、フィルターによって 1 つのエンティティが選択されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="ca6f3-231">そのため、`Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="ca6f3-232">`Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="ca6f3-233">`CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ca6f3-235">コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="ca6f3-236">コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="ca6f3-237">代わりに、コレクションが空の場合に既定値 (この場合は null) を返す `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="ca6f3-238">次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="ca6f3-239">Instructors/Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-239">Update the instructors Index page</span></span>

<span data-ttu-id="ca6f3-240">次のコードを使用して *Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="ca6f3-241">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ca6f3-242">`page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="ca6f3-243">`"{id:int?}"` はルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="ca6f3-244">ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="ca6f3-245">たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="ca6f3-246">ページ ディレクティブが `@page "{id:int?}"` の場合、URL は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="ca6f3-247">`item.OfficeAssignment` が null ではない場合にのみ、`item.OfficeAssignment.Location` を表示する **Office** 列を追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="ca6f3-248">これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="ca6f3-249">インストラクターごとに担当したコースを表示する **Courses** 列を追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="ca6f3-250">この Razor 構文の詳細については、「[Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-)」(@: による明示的な行の遷移) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-250">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="ca6f3-251">選択したインストラクターとコースの `tr` 要素に `class="success"` を動的に追加するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="ca6f3-252">これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="ca6f3-253">**Select** とラベル付けされる新しいハイパーリンクを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="ca6f3-254">このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="ca6f3-255">選択した Instructor のコースのテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="ca6f3-256">選択したコースの学生登録のテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="ca6f3-257">アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="ca6f3-258">`OfficeAssignment` が null の場合、空のテーブル セルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="ca6f3-259">インストラクターの **Select** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="ca6f3-260">行スタイルの変更とそのインストラクターに割り当てられたコースが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="ca6f3-261">コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-261">Select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="ca6f3-263">Single を使用する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-263">Using Single</span></span>

<span data-ttu-id="ca6f3-264">`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="ca6f3-265">Where 条件を使った `Single` の使用は、個人の好みの問題です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="ca6f3-266">`Where` メソッドを使用しても利点はありません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="ca6f3-267">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="ca6f3-267">Explicit loading</span></span>

<span data-ttu-id="ca6f3-268">現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="ca6f3-269">ユーザーがコースの登録を表示することはほとんどないとします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="ca6f3-270">その場合、最適化は要求された場合にのみ登録データを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="ca6f3-271">このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="ca6f3-272">次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="ca6f3-273">上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="ca6f3-274">コースが選択されると、明示的読み込みコードで以下が取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="ca6f3-275">選択したコースの `Enrollment` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="ca6f3-276">各 `Enrollment` の `Student` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="ca6f3-277">上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="ca6f3-278">追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="ca6f3-279">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-279">Test the app.</span></span> <span data-ttu-id="ca6f3-280">ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ca6f3-281">次の手順</span><span class="sxs-lookup"><span data-stu-id="ca6f3-281">Next steps</span></span>

<span data-ttu-id="ca6f3-282">次のチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="ca6f3-283">[前のチュートリアル](xref:data/ef-rp/complex-data-model)
>[次のチュートリアル](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca6f3-284">このチュートリアルでは、関連データが読み取られ、表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="ca6f3-285">関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="ca6f3-286">解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="ca6f3-287">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ca6f3-288">以下の図は、このチュートリアルの完成したページを示しています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-288">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="ca6f3-291">関連データの一括読み込み、明示的読み込み、遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="ca6f3-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="ca6f3-292">EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="ca6f3-293">[一括読み込み](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="ca6f3-294">一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="ca6f3-295">エンティティが読み取られるときに、その関連データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="ca6f3-296">これは通常、必要なすべてのデータを取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="ca6f3-297">EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="ca6f3-298">複数のクエリを発行することで、1 つのクエリしかなかった EF6 の一部のクエリよりも、効率を高めることができます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="ca6f3-299">一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="ca6f3-301">一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="ca6f3-302">メイン クエリに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="ca6f3-302">One query for the main query</span></span> 
  * <span data-ttu-id="ca6f3-303">読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="ca6f3-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="ca6f3-304">`Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="ca6f3-305">"修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="ca6f3-306">`Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="ca6f3-308">メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="ca6f3-309">ナビゲーション プロパティのデータが明示的に含まれ*ない*場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="ca6f3-310">[明示的読み込み](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="ca6f3-311">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ca6f3-312">必要なときに関連するデータを取得するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="ca6f3-313">分離したクエリによる明示的読み込みにより、複数のクエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="ca6f3-314">明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="ca6f3-315">明示的読み込みを行うには、`Load` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="ca6f3-316">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-316">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="ca6f3-318">[遅延読み込み](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ca6f3-319">[遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ca6f3-320">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ca6f3-321">ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="ca6f3-322">初めてナビゲーション プロパティにアクセスされるたびに、クエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="ca6f3-323">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="ca6f3-324">部門名を表示する Course ページを作成する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="ca6f3-325">Course エンティティには、`Department` エンティティを含むナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="ca6f3-326">`Department` エンティティには、コースが割り当てられる部門が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="ca6f3-327">コースの一覧で割り当てられている部門の名前を表示するには:</span><span class="sxs-lookup"><span data-stu-id="ca6f3-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="ca6f3-328">`Department` エンティティから `Name` プロパティを取得します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="ca6f3-329">`Department` エンティティは `Course.Department` ナビゲーション プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="ca6f3-331">Course モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="ca6f3-331">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca6f3-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca6f3-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ca6f3-333">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Course` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca6f3-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca6f3-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="ca6f3-335">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-335">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="ca6f3-336">上記のコマンドは、`Course` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="ca6f3-337">Visual Studio でプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="ca6f3-338">*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="ca6f3-339">スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="ca6f3-340">`Include` メソッドが一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="ca6f3-341">アプリを実行し、 **[Courses]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="ca6f3-342">Department 列に `DepartmentID` が表示されますが、これには役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="ca6f3-343">`OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="ca6f3-344">上のコードは `AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="ca6f3-345">`AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="ca6f3-346">これらのエンティティは現在のコンテキストでは更新されないため、追跡されません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="ca6f3-347">次の強調表示されているマークアップで *Pages/Courses/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="ca6f3-348">スキャフォールディング コードに、次の変更が行われました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="ca6f3-349">見出しが Index から Courses に変更されました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="ca6f3-350">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="ca6f3-351">既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="ca6f3-352">ただし、このケースでは、主キーは意味があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="ca6f3-353">部門名が表示されるように、**Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="ca6f3-354">コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="ca6f3-355">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="ca6f3-357">Select を使用した関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="ca6f3-357">Loading related data with Select</span></span>

<span data-ttu-id="ca6f3-358">`OnGetAsync` メソッドは、`Include` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="ca6f3-359">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="ca6f3-360">`Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="ca6f3-361">コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="ca6f3-362">次のコードは、`Select` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="ca6f3-363">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="ca6f3-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="ca6f3-364">完全な例については、[IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-364">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="ca6f3-365">コース登録を示す Instructors ページを作成する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="ca6f3-366">このセクションでは、Instructors ページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-366">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="ca6f3-367">![Instructors/Index ページ](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-367">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="ca6f3-368">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="ca6f3-369">インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="ca6f3-370">`Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ca6f3-371">`OfficeAssignment` エンティティには一括読み込みが使用されています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="ca6f3-372">一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="ca6f3-373">この場合、インストラクターへのオフィスの割り当てが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="ca6f3-374">ユーザーがインストラクターを選択 (上の図では Harui) すると、関連 `Course` エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="ca6f3-375">`Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="ca6f3-376">`Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="ca6f3-377">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="ca6f3-378">この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="ca6f3-379">ユーザーがコースを選択すると (上の図では Chemistry (化学))、`Enrollments` エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="ca6f3-380">上の図では、受講者名とグレードが表示されています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="ca6f3-381">`Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="ca6f3-382">Instructor インデックス ビューのビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="ca6f3-383">Instructors ページには、3 つの異なるテーブルからのデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="ca6f3-384">3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="ca6f3-385">次のコードを使用して、*SchoolViewModels* フォルダー内に *InstructorIndexData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="ca6f3-386">Instructor モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="ca6f3-386">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca6f3-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca6f3-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ca6f3-388">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Instructor` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca6f3-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca6f3-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="ca6f3-390">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-390">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="ca6f3-391">上記のコマンドは、`Instructor` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="ca6f3-392">アプリを実行し、Instructors ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="ca6f3-393">*Pages/Instructors/Index.cshtml.cs* を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="ca6f3-394">`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="ca6f3-395">*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="ca6f3-396">クエリには次の 2 つが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-396">The query has two includes:</span></span>

* <span data-ttu-id="ca6f3-397">`OfficeAssignment`:[Instructors ビュー](#IP)に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="ca6f3-398">`CourseAssignments`:担当したコースを取り込みます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="ca6f3-399">Instructors/Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-399">Update the instructors Index page</span></span>

<span data-ttu-id="ca6f3-400">次のマークアップを使用して *Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="ca6f3-401">上記のマークアップは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ca6f3-402">`page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="ca6f3-403">`"{id:int?}"` はルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="ca6f3-404">ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="ca6f3-405">たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="ca6f3-406">ページ ディレクティブが `@page "{id:int?}"` の場合、上記の URL は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="ca6f3-407">ページ タイトルは **Instructors** です。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="ca6f3-408">`item.OfficeAssignment` が null ではない場合にのみ `item.OfficeAssignment.Location` を表示する **Office** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="ca6f3-409">これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="ca6f3-410">インストラクターごとに担当したコースを表示する **Courses** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="ca6f3-411">この Razor 構文の詳細については、「[@: による明示的な行の遷移`@:`](xref:mvc/views/razor#explicit-line-transition-with-)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-411">See [Explicit line transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="ca6f3-412">選択したインストラクターの `tr` 要素に `class="success"` を動的に追加するコードを追加しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="ca6f3-413">これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="ca6f3-414">**Select** とラベル付けされるハイパーリンクを追加しました。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="ca6f3-415">このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="ca6f3-416">アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="ca6f3-417">OfficeAssignment\` が null の場合、空のテーブル セルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="ca6f3-418">**[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-418">Click on the **Select** link.</span></span> <span data-ttu-id="ca6f3-419">行のスタイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="ca6f3-420">選択したインストラクターが担当するコースを追加する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="ca6f3-421">次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="ca6f3-422">`public int CourseID { get; set; }` を追加します</span><span class="sxs-lookup"><span data-stu-id="ca6f3-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="ca6f3-423">更新されたクエリを確認します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="ca6f3-424">上記のクエリは `Department` エンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="ca6f3-425">次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="ca6f3-426">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="ca6f3-427">ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="ca6f3-428">`Where` メソッドはコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="ca6f3-429">上記の `Where` メソッドでは、1 つの `Instructor` エンティティのみが返されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="ca6f3-430">`Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="ca6f3-431">`Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="ca6f3-432">`CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ca6f3-434">コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="ca6f3-435">コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="ca6f3-436">代わりに、コレクションが空の場合に既定値を返す (この場合は null) `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="ca6f3-437">空のコレクションで `SingleOrDefault` を使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="ca6f3-438">(null 参照で `Courses` プロパティを見つけようとすると) 例外になります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="ca6f3-439">例外メッセージに問題の原因が明確に示されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="ca6f3-440">次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="ca6f3-441">*Pages/Instructors/Index.cshtml* Razor ページの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="ca6f3-442">上記のマークアップは、インストラクターが選択されたときに、インストラクターに関連するコースのリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="ca6f3-443">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-443">Test the app.</span></span> <span data-ttu-id="ca6f3-444">Instructors ページの **[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="ca6f3-445">受講者データを表示する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-445">Show student data</span></span>

<span data-ttu-id="ca6f3-446">このセクションでは、選択したコースの受講者データを表示するため、アプリが更新されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="ca6f3-447">次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドのクエリを更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="ca6f3-448">*Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="ca6f3-449">ファイルの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="ca6f3-450">上記のマークアップは、選択したコースに登録されている受講者のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="ca6f3-451">ページを更新し、インストラクターを選択します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="ca6f3-452">コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-452">Select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="ca6f3-454">Single を使用する</span><span class="sxs-lookup"><span data-stu-id="ca6f3-454">Using Single</span></span>

<span data-ttu-id="ca6f3-455">`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="ca6f3-456">上記の `Single` アプローチでは、`Where` を使用すること以上のメリットは提供されません。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="ca6f3-457">一部の開発者は、`Single` アプローチ スタイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="ca6f3-458">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="ca6f3-458">Explicit loading</span></span>

<span data-ttu-id="ca6f3-459">現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="ca6f3-460">ユーザーがコースの登録を表示することはほとんどないとします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="ca6f3-461">その場合、最適化は要求された場合にのみ登録データを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="ca6f3-462">このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="ca6f3-463">次のコードを使用して `OnGetAsync` を更新します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="ca6f3-464">上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="ca6f3-465">コースが選択されると、強調表示されたコードが以下を取得します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="ca6f3-466">選択したコースの `Enrollment` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="ca6f3-467">各 `Enrollment` の `Student` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="ca6f3-468">上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="ca6f3-469">追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="ca6f3-470">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-470">Test the app.</span></span> <span data-ttu-id="ca6f3-471">ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="ca6f3-472">次のチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ca6f3-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca6f3-473">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ca6f3-473">Additional resources</span></span>

* [<span data-ttu-id="ca6f3-474">このチュートリアルの YouTube バージョン (part1)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="ca6f3-475">このチュートリアルの YouTube バージョン (part2)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="ca6f3-476">[前へ](xref:data/ef-rp/complex-data-model)
>[次へ](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="ca6f3-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
