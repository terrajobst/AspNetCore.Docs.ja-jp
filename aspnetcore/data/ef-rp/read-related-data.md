---
title: ASP.NET Core の Razor ページと EF Core - 関連データの読み込み - 6/8
author: rick-anderson
description: このチュートリアルでは、関連データ (Entity Framework がナビゲーション プロパティに読み込むデータ) の読み取りと表示を行います。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 93fbb4741f476368d75d23162d6e2597de7b263e
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819920"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="1c8b6-103">ASP.NET Core の Razor ページと EF Core - 関連データの読み込み - 6/8</span><span class="sxs-lookup"><span data-stu-id="1c8b6-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="1c8b6-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c8b6-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="1c8b6-105">このチュートリアルでは、関連データが読み取られ、表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-105">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="1c8b6-106">関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1c8b6-107">解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-107">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="1c8b6-108">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="1c8b6-108">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="1c8b6-109">以下の図は、このチュートリアルの完成したページを示しています。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-109">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="1c8b6-112">関連データの一括読み込み、明示的読み込み、遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="1c8b6-112">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="1c8b6-113">EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-113">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1c8b6-114">[一括読み込み](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-114">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1c8b6-115">一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-115">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1c8b6-116">エンティティが読み取られるときに、その関連データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-116">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1c8b6-117">これは通常、必要なすべてのデータを取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-117">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1c8b6-118">EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-118">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1c8b6-119">複数のクエリを発行することで、1 つのクエリしかなかった EF6 の一部のクエリよりも、効率を高めることができます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-119">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="1c8b6-120">一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-120">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="1c8b6-122">一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-122">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1c8b6-123">メイン クエリに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="1c8b6-123">One query for the main query</span></span> 
  * <span data-ttu-id="1c8b6-124">読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="1c8b6-124">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1c8b6-125">`Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-125">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1c8b6-126">"修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-126">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1c8b6-127">`Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-127">Separate queries with `Load` is more like explict loading than eager loading.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1c8b6-129">メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-129">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1c8b6-130">ナビゲーション プロパティのデータが明示的に含まれ*ない*場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-130">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1c8b6-131">[明示的読み込み](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-131">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1c8b6-132">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-132">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1c8b6-133">必要なときに関連するデータを取得するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-133">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1c8b6-134">分離したクエリによる明示的読み込みにより、複数のクエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-134">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="1c8b6-135">明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-135">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1c8b6-136">明示的読み込みを行うには、`Load` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-136">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1c8b6-137">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-137">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1c8b6-139">[遅延読み込み](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-139">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1c8b6-140">[遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-140">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1c8b6-141">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-141">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1c8b6-142">ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-142">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1c8b6-143">初めてナビゲーション プロパティにアクセスされるたびに、クエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-143">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="1c8b6-144">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-144">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="1c8b6-145">部門名を表示する Course ページを作成する</span><span class="sxs-lookup"><span data-stu-id="1c8b6-145">Create a Course page that displays department name</span></span>

<span data-ttu-id="1c8b6-146">Course エンティティには、`Department` エンティティを含むナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-146">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="1c8b6-147">`Department` エンティティには、コースが割り当てられる部門が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-147">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="1c8b6-148">コースの一覧で割り当てられている部門の名前を表示するには:</span><span class="sxs-lookup"><span data-stu-id="1c8b6-148">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="1c8b6-149">`Department` エンティティから `Name` プロパティを取得します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-149">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="1c8b6-150">`Department` エンティティは `Course.Department` ナビゲーション プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-150">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="1c8b6-152">Course モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="1c8b6-152">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c8b6-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c8b6-153">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1c8b6-154">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Course` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-154">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1c8b6-155">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1c8b6-155">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="1c8b6-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="1c8b6-157">上記のコマンドは、`Course` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-157">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="1c8b6-158">Visual Studio でプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-158">Open the project in Visual Studio.</span></span>

<span data-ttu-id="1c8b6-159">*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1c8b6-160">スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1c8b6-161">`Include` メソッドが一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-161">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="1c8b6-162">アプリを実行し、 **[Courses]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1c8b6-163">Department 列に `DepartmentID` が表示されますが、これには役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="1c8b6-164">`OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-164">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="1c8b6-165">上のコードは `AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-165">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="1c8b6-166">`AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-166">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1c8b6-167">これらのエンティティは現在のコンテキストでは更新されないため、追跡されません。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-167">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1c8b6-168">次の強調表示されているマークアップで *Pages/Courses/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-168">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="1c8b6-169">スキャフォールディング コードに、次の変更が行われました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-169">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1c8b6-170">見出しが Index から Courses に変更されました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-170">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="1c8b6-171">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-171">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1c8b6-172">既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-172">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1c8b6-173">ただし、このケースでは、主キーは意味があります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-173">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1c8b6-174">部門名が表示されるように、**Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-174">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1c8b6-175">コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-175">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1c8b6-176">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-176">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1c8b6-178">Select を使用した関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="1c8b6-178">Loading related data with Select</span></span>

<span data-ttu-id="1c8b6-179">`OnGetAsync` メソッドは、`Include` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-179">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1c8b6-180">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-180">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="1c8b6-181">`Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-181">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="1c8b6-182">コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-182">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1c8b6-183">次のコードは、`Select` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-183">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1c8b6-184">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="1c8b6-184">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="1c8b6-185">完全な例については、[IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-185">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="1c8b6-186">コース登録を示す Instructors ページを作成する</span><span class="sxs-lookup"><span data-stu-id="1c8b6-186">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="1c8b6-187">このセクションでは、Instructors ページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-187">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="1c8b6-188">![Instructors/Index ページ](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="1c8b6-188">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="1c8b6-189">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-189">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1c8b6-190">インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-190">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1c8b6-191">`Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-191">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1c8b6-192">`OfficeAssignment` エンティティには一括読み込みが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-192">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1c8b6-193">一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-193">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1c8b6-194">この場合、インストラクターへのオフィスの割り当てが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-194">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1c8b6-195">ユーザーがインストラクターを選択 (上の図では Harui) すると、関連 `Course` エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-195">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="1c8b6-196">`Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-196">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1c8b6-197">`Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-197">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1c8b6-198">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-198">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1c8b6-199">この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-199">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1c8b6-200">ユーザーがコースを選択すると (上の図では Chemistry (化学))、`Enrollments` エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-200">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1c8b6-201">上の図では、受講者名とグレードが表示されています。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-201">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1c8b6-202">`Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-202">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="1c8b6-203">Instructor インデックス ビューのビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="1c8b6-203">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="1c8b6-204">Instructors ページには、3 つの異なるテーブルからのデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-204">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1c8b6-205">3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-205">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="1c8b6-206">次のコードを使用して、*SchoolViewModels* フォルダー内に *InstructorIndexData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-206">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="1c8b6-207">Instructor モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="1c8b6-207">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c8b6-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c8b6-208">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1c8b6-209">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Instructor` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-209">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1c8b6-210">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1c8b6-210">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="1c8b6-211">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-211">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1c8b6-212">上記のコマンドは、`Instructor` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-212">The preceding command scaffolds the `Instructor` model.</span></span> <span data-ttu-id="1c8b6-213">アプリを実行し、Instructors ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-213">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="1c8b6-214">*Pages/Instructors/Index.cshtml.cs* を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-214">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="1c8b6-215">`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-215">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1c8b6-216">*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-216">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1c8b6-217">クエリには次の 2 つが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-217">The query has two includes:</span></span>

* <span data-ttu-id="1c8b6-218">`OfficeAssignment`:[Instructors ビュー](#IP)に表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-218">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="1c8b6-219">`CourseAssignments`:担当したコースを取り込みます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-219">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1c8b6-220">Instructors/Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="1c8b6-220">Update the instructors Index page</span></span>

<span data-ttu-id="1c8b6-221">次のマークアップを使用して *Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-221">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="1c8b6-222">上記のマークアップは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-222">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="1c8b6-223">`page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-223">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1c8b6-224">`"{id:int?}"` はルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-224">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1c8b6-225">ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-225">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1c8b6-226">たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-226">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="1c8b6-227">ページ ディレクティブが `@page "{id:int?}"` の場合、上記の URL は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-227">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="1c8b6-228">ページ タイトルは **Instructors** です。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-228">Page title is **Instructors**.</span></span>
* <span data-ttu-id="1c8b6-229">`item.OfficeAssignment` が null ではない場合にのみ `item.OfficeAssignment.Location` を表示する **Office** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-229">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1c8b6-230">これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-230">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="1c8b6-231">インストラクターごとに担当したコースを表示する **Courses** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-231">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1c8b6-232">この Razor 構文の詳細については、「[@: による明示的な行の遷移`@:`](xref:mvc/views/razor#explicit-line-transition-with-)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-232">See [Explicit line transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="1c8b6-233">選択したインストラクターの `tr` 要素に `class="success"` を動的に追加するコードを追加しました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-233">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="1c8b6-234">これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-234">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="1c8b6-235">**Select** とラベル付けされるハイパーリンクを追加しました。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-235">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1c8b6-236">このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-236">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="1c8b6-237">アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-237">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1c8b6-238">OfficeAssignment\` が null の場合、空のテーブル セルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-238">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

![何も選択されていない Instructors/Index ページ](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="1c8b6-240">**[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-240">Click on the **Select** link.</span></span> <span data-ttu-id="1c8b6-241">行のスタイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-241">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="1c8b6-242">選択したインストラクターが担当するコースを追加する</span><span class="sxs-lookup"><span data-stu-id="1c8b6-242">Add courses taught by selected instructor</span></span>

<span data-ttu-id="1c8b6-243">次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-243">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="1c8b6-244">`public int CourseID { get; set; }` を追加します</span><span class="sxs-lookup"><span data-stu-id="1c8b6-244">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="1c8b6-245">更新されたクエリを確認します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-245">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1c8b6-246">上記のクエリは `Department` エンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-246">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="1c8b6-247">次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-247">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="1c8b6-248">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-248">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1c8b6-249">ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-249">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="1c8b6-250">`Where` メソッドはコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-250">The `Where` method returns a collection.</span></span> <span data-ttu-id="1c8b6-251">上記の `Where` メソッドでは、1 つの `Instructor` エンティティのみが返されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-251">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="1c8b6-252">`Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-252">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1c8b6-253">`Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-253">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="1c8b6-254">`CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-254">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="1c8b6-256">コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-256">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1c8b6-257">コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-257">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1c8b6-258">代わりに、コレクションが空の場合に既定値を返す (この場合は null) `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-258">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="1c8b6-259">空のコレクションで `SingleOrDefault` を使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-259">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="1c8b6-260">(null 参照で `Courses` プロパティを見つけようとすると) 例外になります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-260">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="1c8b6-261">例外メッセージに問題の原因が明確に示されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-261">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="1c8b6-262">次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-262">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="1c8b6-263">*Pages/Instructors/Index.cshtml* Razor ページの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-263">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="1c8b6-264">上記のマークアップは、インストラクターが選択されたときに、インストラクターに関連するコースのリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-264">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="1c8b6-265">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-265">Test the app.</span></span> <span data-ttu-id="1c8b6-266">Instructors ページの **[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-266">Click on a **Select** link on the instructors page.</span></span>

![インストラクターが選択された Instructors/Index ページ](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a><span data-ttu-id="1c8b6-268">受講者データを表示する</span><span class="sxs-lookup"><span data-stu-id="1c8b6-268">Show student data</span></span>

<span data-ttu-id="1c8b6-269">このセクションでは、選択したコースの受講者データを表示するため、アプリが更新されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-269">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="1c8b6-270">次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドのクエリを更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-270">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1c8b6-271">*Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-271">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="1c8b6-272">ファイルの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-272">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="1c8b6-273">上記のマークアップは、選択したコースに登録されている受講者のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-273">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="1c8b6-274">ページを更新し、インストラクターを選択します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-274">Refresh the page and select an instructor.</span></span> <span data-ttu-id="1c8b6-275">コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-275">Select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="1c8b6-277">Single を使用する</span><span class="sxs-lookup"><span data-stu-id="1c8b6-277">Using Single</span></span>

<span data-ttu-id="1c8b6-278">`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-278">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="1c8b6-279">上記の `Single` アプローチでは、`Where` を使用すること以上のメリットは提供されません。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-279">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="1c8b6-280">一部の開発者は、`Single` アプローチ スタイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-280">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="1c8b6-281">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="1c8b6-281">Explicit loading</span></span>

<span data-ttu-id="1c8b6-282">現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-282">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1c8b6-283">ユーザーがコースの登録を表示することはほとんどないとします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-283">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="1c8b6-284">その場合、最適化は要求された場合にのみ登録データを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-284">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="1c8b6-285">このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-285">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="1c8b6-286">次のコードを使用して `OnGetAsync` を更新します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-286">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="1c8b6-287">上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-287">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="1c8b6-288">コースが選択されると、強調表示されたコードが以下を取得します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-288">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="1c8b6-289">選択したコースの `Enrollment` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-289">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="1c8b6-290">各 `Enrollment` の `Student` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-290">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="1c8b6-291">上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-291">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="1c8b6-292">追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-292">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="1c8b6-293">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-293">Test the app.</span></span> <span data-ttu-id="1c8b6-294">ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-294">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="1c8b6-295">次のチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="1c8b6-295">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c8b6-296">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1c8b6-296">Additional resources</span></span>

* [<span data-ttu-id="1c8b6-297">このチュートリアルの YouTube バージョン (part1)</span><span class="sxs-lookup"><span data-stu-id="1c8b6-297">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="1c8b6-298">このチュートリアルの YouTube バージョン (part2)</span><span class="sxs-lookup"><span data-stu-id="1c8b6-298">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="1c8b6-299">[前へ](xref:data/ef-rp/complex-data-model)
>[次へ](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="1c8b6-299">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>
