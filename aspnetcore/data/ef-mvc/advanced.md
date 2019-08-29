---
title: 'チュートリアル: 高度なシナリオについて学習する - ASP.NET MVC と EF Core'
description: このチュートリアルでは、Entity Framework Core を使用するより高度な ASP.NET Core Web アプリを開発する際に、活用できるいくつかのトピックを紹介します。
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: 913511948def86cb2065459a3fcf5f16362a92d5
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975031"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="6162f-103">チュートリアル: 高度なシナリオについて学習する - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="6162f-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="6162f-104">前のチュートリアルでは、Table-Per-Hierarchy 継承を実装しました。</span><span class="sxs-lookup"><span data-stu-id="6162f-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="6162f-105">このチュートリアルでは、Entity Framework Core を使用するより高度な ASP.NET Core Web アプリケーションを開発する際に、注意すべきいくつかのトピックを紹介します。</span><span class="sxs-lookup"><span data-stu-id="6162f-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="6162f-106">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="6162f-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6162f-107">生 SQL クエリを実行する</span><span class="sxs-lookup"><span data-stu-id="6162f-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="6162f-108">エンティティを返すクエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="6162f-108">Call a query to return entities</span></span>
> * <span data-ttu-id="6162f-109">その他の型を返すクエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="6162f-109">Call a query to return other types</span></span>
> * <span data-ttu-id="6162f-110">更新クエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="6162f-110">Call an update query</span></span>
> * <span data-ttu-id="6162f-111">SQL クエリを調べる</span><span class="sxs-lookup"><span data-stu-id="6162f-111">Examine SQL queries</span></span>
> * <span data-ttu-id="6162f-112">抽象化レイヤーを作成する</span><span class="sxs-lookup"><span data-stu-id="6162f-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="6162f-113">変更の自動検出について学習する</span><span class="sxs-lookup"><span data-stu-id="6162f-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="6162f-114">EF Core のソース コードと開発計画について学習する</span><span class="sxs-lookup"><span data-stu-id="6162f-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="6162f-115">動的な LINQ を使ってコードを簡略化する方法を学習する</span><span class="sxs-lookup"><span data-stu-id="6162f-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6162f-116">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6162f-116">Prerequisites</span></span>

* [<span data-ttu-id="6162f-117">継承の実装</span><span class="sxs-lookup"><span data-stu-id="6162f-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="6162f-118">生 SQL クエリを実行する</span><span class="sxs-lookup"><span data-stu-id="6162f-118">Perform raw SQL queries</span></span>

<span data-ttu-id="6162f-119">Entity Framework を使用する利点の 1 つは、データを格納する特定のメソッドにコードを過度に接近させなくてもよい点です。</span><span class="sxs-lookup"><span data-stu-id="6162f-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="6162f-120">SQL クエリとコマンドが生成されるため、自分でこれらを記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="6162f-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="6162f-121">ただし、例外的なシナリオがあります。手動で作成した特定の SQL クエリを実行する必要がある場合です。</span><span class="sxs-lookup"><span data-stu-id="6162f-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="6162f-122">このようなシナリオでは、Entity Framework Code First API には、SQL コマンドをデータベースに直接渡せるメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6162f-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="6162f-123">EF Core 1.0 には次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="6162f-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="6162f-124">エンティティ型を返すクエリに対して `DbSet.FromSql` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6162f-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="6162f-125">返されたオブジェクトは、`DbSet` オブジェクトで想定されている型である必要があります。また、[追跡をオフにしている場合を除き](crud.md#no-tracking-queries)、データベース コンテキストによって自動的に追跡されます。</span><span class="sxs-lookup"><span data-stu-id="6162f-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="6162f-126">非クエリ コマンドに対して `Database.ExecuteSqlCommand` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6162f-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="6162f-127">エンティティではない型を返すクエリを実行する必要がある場合は、ADO.NET と EF で提供されるデータベース接続を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6162f-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="6162f-128">このメソッドを使用してエンティティ型を取得する場合でも、返されるデータはデータベース コンテキストによって追跡されません。</span><span class="sxs-lookup"><span data-stu-id="6162f-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="6162f-129">Web アプリケーションで SQL コマンドを実行する場合は常に、SQL インジェクション攻撃から自身のサイトを保護する対策を講じる必要があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="6162f-130">これを行う 1 つの方法として、パラメーター化されたクエリを使用して、Web ページによって送信された文字列が SQL コマンドとして解釈できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6162f-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="6162f-131">このチュートリアルでは、ユーザー入力をクエリに統合するときに、パラメーター化されたクエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="6162f-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="6162f-132">エンティティを返すクエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="6162f-132">Call a query to return entities</span></span>

<span data-ttu-id="6162f-133">`DbSet<TEntity>` クラスは、`TEntity` 型のエンティティを返すクエリの実行に使用できるメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="6162f-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="6162f-134">このしくみを確認するため、Department (部門) コントローラーの `Details` メソッド内のコードを変更します。</span><span class="sxs-lookup"><span data-stu-id="6162f-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="6162f-135">*DepartmentsController.cs* の `Details` メソッドで、次の強調表示されたコードに示されているように、部門を取得するコードを `FromSql` メソッドの呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6162f-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="6162f-136">新しいコードが正しく動作することを確認するには、 **[Departments]\(部門\)** タブを選択し、いずれかの部門の **[Details]\(詳細\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6162f-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![部門の詳細](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="6162f-138">その他の型を返すクエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="6162f-138">Call a query to return other types</span></span>

<span data-ttu-id="6162f-139">以前に、登録日ごとの学生数を示す About ページ用に、学生の統計グリッドを作成しました。</span><span class="sxs-lookup"><span data-stu-id="6162f-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="6162f-140">Students エンティティ セット (`_context.Students`) からデータを取得し、LINQ を使用して結果を `EnrollmentDateGroup` ビュー モデル オブジェクトに投影しました。</span><span class="sxs-lookup"><span data-stu-id="6162f-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="6162f-141">LINQ を使用するのではなく、SQL そのものを記述するとします。</span><span class="sxs-lookup"><span data-stu-id="6162f-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="6162f-142">これを行うには、エンティティ オブジェクト以外のものを返す SQL クエリを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="6162f-143">EF Core 1.0 では、これを行う方法の 1 つとして、ADO.NET コードを記述し、EF からデータベース接続を取得します。</span><span class="sxs-lookup"><span data-stu-id="6162f-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="6162f-144">*HomeController.cs* で、`About` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6162f-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="6162f-145">using ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="6162f-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="6162f-146">アプリを実行して [About] ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6162f-146">Run the app and go to the About page.</span></span> <span data-ttu-id="6162f-147">以前に行ったのと同じデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6162f-147">It displays the same data it did before.</span></span>

![About ページ](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="6162f-149">更新クエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="6162f-149">Call an update query</span></span>

<span data-ttu-id="6162f-150">Contoso University の管理者が、すべてのコースの単位数を変更するなどの、データベースでグローバルな変更を実行するとします。</span><span class="sxs-lookup"><span data-stu-id="6162f-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="6162f-151">大学に多くのコースがある場合は、それらすべてをエンティティとして取得し、それらを個別に変更するのは非効率的です。</span><span class="sxs-lookup"><span data-stu-id="6162f-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="6162f-152">このセクションでは、すべてのコースの単位数を変更する係数をユーザーが指定できるようにする Web ページを実装し、SQL UPDATE ステートメントを実行することによって変更を行います。</span><span class="sxs-lookup"><span data-stu-id="6162f-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="6162f-153">Web ページは次の図のようになります。</span><span class="sxs-lookup"><span data-stu-id="6162f-153">The web page will look like the following illustration:</span></span>

![Course Credits ページを更新する](advanced/_static/update-credits.png)

<span data-ttu-id="6162f-155">*CoursesController.cs* で、HttpGet および HttpPost に UpdateCourseCredits メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="6162f-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="6162f-156">コントローラーが HttpGet 要求を処理するときに、`ViewData["RowsAffected"]` では何も返されず、前の図に示されているように、ビューに空のテキスト ボックスと、[送信] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6162f-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="6162f-157">**[更新]** ボタンをクリックすると、HttpPost メソッドが呼び出され、乗数がテキスト ボックスに入力した値になります。</span><span class="sxs-lookup"><span data-stu-id="6162f-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="6162f-158">このコードは次に、コースを更新し、影響を受けた行の数を`ViewData` のビューに返す SQL を実行します。</span><span class="sxs-lookup"><span data-stu-id="6162f-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="6162f-159">ビューが `RowsAffected` 値を取得すると、更新された行の数を表示します。</span><span class="sxs-lookup"><span data-stu-id="6162f-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="6162f-160">**ソリューション エクスプローラー**で、*Views/Courses* フォルダーを右クリックし、 **[追加] > [新しい項目]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="6162f-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="6162f-161">**[新しい項目の追加]** ダイアログで、左側のウィンドウの **[インストール済み]** の下の **[ASP.NET Core]** をクリックし、 **[Razor ビュー]** をクリックして、新しいビューに *UpdateCourseCredits.cshtml* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="6162f-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="6162f-162">*Views/Courses/UpdateCourseCredits.cshtml* で、テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6162f-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="6162f-163">**[Courses]\(コース\)** タブを選択してから、ブラウザーのアドレス バーで URL の末尾に "/UpdateCourseCredits" を追加して (例: `http://localhost:5813/Courses/UpdateCourseCredits`)、`UpdateCourseCredits` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6162f-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="6162f-164">テキスト ボックスに数値を入力します。</span><span class="sxs-lookup"><span data-stu-id="6162f-164">Enter a number in the text box:</span></span>

![Course Credits ページを更新する](advanced/_static/update-credits.png)

<span data-ttu-id="6162f-166">**[更新]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6162f-166">Click **Update**.</span></span> <span data-ttu-id="6162f-167">影響を受けた行の数が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6162f-167">You see the number of rows affected:</span></span>

![Course Credits ページの更新で影響を受けた行](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="6162f-169">**[リストに戻る]** をクリックして、単位数が変更されたコースの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="6162f-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="6162f-170">実稼働コードでは、更新の結果が常に有効なデータになることが保証される点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="6162f-171">ここに示した簡略化されたコードは、5 より大きい数値になるように単位数を乗算できます</span><span class="sxs-lookup"><span data-stu-id="6162f-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="6162f-172">(`Credits` プロパティには `[Range(0, 5)]` 属性があります)。更新クエリは機能しますが、無効なデータによって、5 以下の単位数を想定していたシステムの他の部分で予期しない結果が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="6162f-173">SQL クエリの詳細については、「[Raw SQL Queries](/ef/core/querying/raw-sql)」 (生 SQL クエリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="6162f-174">SQL クエリを調べる</span><span class="sxs-lookup"><span data-stu-id="6162f-174">Examine SQL queries</span></span>

<span data-ttu-id="6162f-175">データベースに送信される実際の SQL クエリを確認できると役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="6162f-176">ASP.NET Core の組み込みのログ記録機能は、クエリと更新の SQL を含むログを書き込むため、EF Core によって自動的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6162f-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="6162f-177">このセクションでは、SQL ログの例をいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="6162f-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="6162f-178">*StudentsController.cs* を開き、`Details` メソッドで `if (student == null)` ステートメントにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="6162f-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="6162f-179">デバッグ モードでアプリを実行して、学生の [Details] ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6162f-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="6162f-180">デバッグの出力を示す **[出力]** ウィンドウに移動して、クエリを確認します。</span><span class="sxs-lookup"><span data-stu-id="6162f-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="6162f-181">驚くかもしれませんが、SQL は Person テーブルから最大 2 つの行 (`TOP(2)`) を選択します。</span><span class="sxs-lookup"><span data-stu-id="6162f-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="6162f-182">`SingleOrDefaultAsync` メソッドは、サーバー上の 1 行に解決されません。</span><span class="sxs-lookup"><span data-stu-id="6162f-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="6162f-183">その理由を説明します。</span><span class="sxs-lookup"><span data-stu-id="6162f-183">Here's why:</span></span>

* <span data-ttu-id="6162f-184">クエリが複数の行を返すと、メソッドは null を返します。</span><span class="sxs-lookup"><span data-stu-id="6162f-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="6162f-185">クエリが複数の行を返すかどうかを判断するため、EF はクエリが少なくとも 2 を返すかどうかを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="6162f-186">**[出力]** ウィンドウでログ出力を取得するには、デバッグ モードを使用してブレークポイントで停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6162f-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="6162f-187">これは単に、出力を見たいポイントでログを停止する便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="6162f-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="6162f-188">これを行わないと、ログ記録は続行され、関心がある部分までスクロールで戻る必要があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="6162f-189">抽象化レイヤーを作成する</span><span class="sxs-lookup"><span data-stu-id="6162f-189">Create an abstraction layer</span></span>

<span data-ttu-id="6162f-190">多くの開発者は、Entity Framework で動作するコードをラップするラッパーとして、Repository パターンと Unit of Work パターンを実装するためのコードを記述します。</span><span class="sxs-lookup"><span data-stu-id="6162f-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="6162f-191">これらのパターンは、アプリケーションのデータ アクセス層とビジネス ロジック層の間に抽象化レイヤーを作成するためのものです。</span><span class="sxs-lookup"><span data-stu-id="6162f-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="6162f-192">これらのパターンを実装すると、データ ストアの変更からアプリケーションを隔離でき、自動化された単体テストやテスト駆動開発 (TDD) を円滑化できます。</span><span class="sxs-lookup"><span data-stu-id="6162f-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="6162f-193">ただし、次の複数の理由により、追加のコードを記述してこれらのパターンを実装することが、EF を使用するアプリケーションにとって最善の選択肢ではない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="6162f-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="6162f-194">EF コンテキスト クラス自体が、コードをデータ ストア固有のコードから隔離します。</span><span class="sxs-lookup"><span data-stu-id="6162f-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="6162f-195">EF コンテキスト クラスは、EF を使用して行っているデータベースの更新の unit-of-work クラスとして動作できます。</span><span class="sxs-lookup"><span data-stu-id="6162f-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="6162f-196">リポジトリ コードを記述しなくても、EF には TDD を実装するための機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6162f-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="6162f-197">Repository パターンと Unit of Work パターンを実装する方法については、[このチュートリアル シリーズの Entity Framework 5 バージョン](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="6162f-198">Entity Framework Core は、テストに使用できる In-Memory データベース プロバイダーを実装します。</span><span class="sxs-lookup"><span data-stu-id="6162f-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="6162f-199">詳細については、[InMemory を使ったテスト](/ef/core/miscellaneous/testing/in-memory)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="6162f-200">変更の自動検出</span><span class="sxs-lookup"><span data-stu-id="6162f-200">Automatic change detection</span></span>

<span data-ttu-id="6162f-201">Entity Framework では、エンティティの現在の値と元の値を比較して、エンティティがどのように変更されたか (およびそれによって、どの更新プログラムをデータベースに送信する必要があるか) を判断します。</span><span class="sxs-lookup"><span data-stu-id="6162f-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="6162f-202">元の値は、エンティティが照会されるかアタッチされるときに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6162f-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="6162f-203">変更の自動検出を行うメソッドには、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="6162f-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="6162f-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="6162f-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="6162f-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="6162f-205">DbContext.Entry</span></span>

* <span data-ttu-id="6162f-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="6162f-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="6162f-207">多数のエンティティを追跡していて、これらのいずれかのメソッドをループ内で何度も呼び出す場合、`ChangeTracker.AutoDetectChangesEnabled` プロパティを使用して変更の自動検出を一時的にオフにすると、パフォーマンスが大幅に向上する場合があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="6162f-208">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6162f-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="6162f-209">EF Core のソース コードと開発計画</span><span class="sxs-lookup"><span data-stu-id="6162f-209">EF Core source code and development plans</span></span>

<span data-ttu-id="6162f-210">Entity Framework Core のソースは、[https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore) にあります。</span><span class="sxs-lookup"><span data-stu-id="6162f-210">The Entity Framework Core source is at [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span></span> <span data-ttu-id="6162f-211">EF Core リポジトリには、夜間ビルド、問題追跡、機能仕様、設計ミーティング メモ、および[将来の開発のためのロードマップ](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap)が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6162f-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span></span> <span data-ttu-id="6162f-212">バグを見つけて報告したり、投稿することができます。</span><span class="sxs-lookup"><span data-stu-id="6162f-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="6162f-213">ソース コードはオープンですが、Entity Framework Core はマイクロソフト製品として完全にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6162f-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="6162f-214">Microsoft Entity Framework チームは、各リリースの品質を保証するため、受け入れる投稿を管理し、すべてのコード変更をテストしています。</span><span class="sxs-lookup"><span data-stu-id="6162f-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="6162f-215">既存のデータベースからのリバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="6162f-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="6162f-216">既存のデータベースからエンティティ クラスを含むデータ モデルをリバース エンジニアリングするには、[scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6162f-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="6162f-217">[入門用チュートリアル](/ef/core/get-started/aspnetcore/existing-db)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="6162f-218">動的な LINQ を使ってコードを簡略化する</span><span class="sxs-lookup"><span data-stu-id="6162f-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="6162f-219">[このシリーズの 3 番目のチュートリアル](sort-filter-page.md)では、`switch`ステートメントで列名をハード コーディングすることで、LINQ コードを記述する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6162f-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="6162f-220">選択する列が 2 つの場合は正常に機能しますが、多数の列がある場合は、コードが冗長になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="6162f-221">この問題を解決するため、`EF.Property` メソッドを使用して、プロパティの名前を文字列として指定できます。</span><span class="sxs-lookup"><span data-stu-id="6162f-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="6162f-222">この方法を試すには、`StudentsController` の `Index` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6162f-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="6162f-223">謝辞</span><span class="sxs-lookup"><span data-stu-id="6162f-223">Acknowledgments</span></span>

<span data-ttu-id="6162f-224">チュートリアルを執筆してくださった、Tom Dykstra と Rick Anderson (twitter @RickAndMSFT)。</span><span class="sxs-lookup"><span data-stu-id="6162f-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="6162f-225">コードの確認をサポートし、チュートリアル用のコードの記述中に発生した問題のデバッグを支援してくれた、Rowan Miller、Diego Vega、およびその他の Entity Framework チームのメンバー。</span><span class="sxs-lookup"><span data-stu-id="6162f-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="6162f-226">ASP.NET Core 2.2 用にチュートリアルの更新作業を行ってくれた、John Parente と Paul Goldman。</span><span class="sxs-lookup"><span data-stu-id="6162f-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="6162f-227">一般的なエラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6162f-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="6162f-228">ContosoUniversity.dll が別のプロセスによって使用されている</span><span class="sxs-lookup"><span data-stu-id="6162f-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="6162f-229">エラー メッセージ</span><span class="sxs-lookup"><span data-stu-id="6162f-229">Error message:</span></span>

> <span data-ttu-id="6162f-230">'...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' を書き込み用に開けません -- '別のプロセスで使用されているため、プロセスはファイル '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' にアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="6162f-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="6162f-231">解決方法 :</span><span class="sxs-lookup"><span data-stu-id="6162f-231">Solution:</span></span>

<span data-ttu-id="6162f-232">IIS express でサイトを停止します。</span><span class="sxs-lookup"><span data-stu-id="6162f-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="6162f-233">Windows システム トレイに戻り、IIS Express を見つけてそのアイコンを右クリックし、Contoso University サイトを選択し、 **[サイトの停止]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6162f-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="6162f-234">Up メソッドと Down メソッドでコードを使用せずに移行がスキャフォールディングされる</span><span class="sxs-lookup"><span data-stu-id="6162f-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="6162f-235">考えられる原因:</span><span class="sxs-lookup"><span data-stu-id="6162f-235">Possible cause:</span></span>

<span data-ttu-id="6162f-236">EF CLI コマンドは、コード ファイルを自動的に閉じて保存しません。</span><span class="sxs-lookup"><span data-stu-id="6162f-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="6162f-237">`migrations add` コマンドを実行するときに未保存の変更があると、EF は変更を検出しません。</span><span class="sxs-lookup"><span data-stu-id="6162f-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="6162f-238">解決方法 :</span><span class="sxs-lookup"><span data-stu-id="6162f-238">Solution:</span></span>

<span data-ttu-id="6162f-239">`migrations remove` コマンドを実行して、コードの変更を保存し、`migrations add` コマンドを再実行します。</span><span class="sxs-lookup"><span data-stu-id="6162f-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="6162f-240">データベースの更新中のエラー</span><span class="sxs-lookup"><span data-stu-id="6162f-240">Errors while running database update</span></span>

<span data-ttu-id="6162f-241">データが存在するデータベースでスキーマの変更を行っているときに、他のエラーが発生する場合があります。</span><span class="sxs-lookup"><span data-stu-id="6162f-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="6162f-242">解決できない移行エラーが発生した場合は、接続文字列のデータベース名を変更するか、データベースを削除できます。</span><span class="sxs-lookup"><span data-stu-id="6162f-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="6162f-243">新しいデータベースには移行するデータが存在しないため、update-database コマンドがエラーなしで完了する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="6162f-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="6162f-244">最も簡単な方法は、*appsettings.json* でデータベースの名前を変更することです。</span><span class="sxs-lookup"><span data-stu-id="6162f-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="6162f-245">次に `database update` を実行したときに、新しいデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6162f-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="6162f-246">SSOX でデータベースを削除するには、そのデータベースを右クリックして、 **[削除]** をクリックしてから、 **[データベースの削除]** ダイアログ ボックスで **[既存の接続を閉じる]** 、 **[OK]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="6162f-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="6162f-247">CLI を使用してデータベースを削除するには、`database drop` CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6162f-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="6162f-248">SQL Server インスタンスの位置を特定しているときのエラー</span><span class="sxs-lookup"><span data-stu-id="6162f-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="6162f-249">エラー メッセージ:</span><span class="sxs-lookup"><span data-stu-id="6162f-249">Error Message:</span></span>

> <span data-ttu-id="6162f-250">SQL Server への接続を確立しているときに、ネットワーク関連またはインスタンス固有のエラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="6162f-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="6162f-251">サーバーが見つからないかアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="6162f-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="6162f-252">インスタンス名が正しいこと、および SQL Server がリモート接続を許可するように構成されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="6162f-253">(プロバイダー:SQL ネットワーク インターフェイス。エラー: 26 - 指定されたサーバーまたはインスタンスの位置を特定しているときにエラーが発生しました)</span><span class="sxs-lookup"><span data-stu-id="6162f-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="6162f-254">解決方法 :</span><span class="sxs-lookup"><span data-stu-id="6162f-254">Solution:</span></span>

<span data-ttu-id="6162f-255">接続文字列を確認します。</span><span class="sxs-lookup"><span data-stu-id="6162f-255">Check the connection string.</span></span> <span data-ttu-id="6162f-256">データベース ファイルを手動で削除した場合は、構築文字列でデータベースの名前を変更して、新しいデータベースで最初からやり直します。</span><span class="sxs-lookup"><span data-stu-id="6162f-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6162f-257">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="6162f-257">Get the code</span></span>

[<span data-ttu-id="6162f-258">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="6162f-258">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="6162f-259">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6162f-259">Additional resources</span></span>

<span data-ttu-id="6162f-260">EF Core の詳細については、「[Entity Framework Core 概要](/ef/core)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="6162f-261">書籍「[Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action)」もご利用いただけます。</span><span class="sxs-lookup"><span data-stu-id="6162f-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="6162f-262">Web アプリの展開方法については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6162f-263">認証および承認などの、ASP.NET Core MVC に関連するその他のトピックについては、「<xref:index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6162f-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6162f-264">次の手順</span><span class="sxs-lookup"><span data-stu-id="6162f-264">Next steps</span></span>

<span data-ttu-id="6162f-265">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="6162f-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6162f-266">生 SQL クエリを実行した</span><span class="sxs-lookup"><span data-stu-id="6162f-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="6162f-267">エンティティを返すクエリを呼び出した</span><span class="sxs-lookup"><span data-stu-id="6162f-267">Called a query to return entities</span></span>
> * <span data-ttu-id="6162f-268">その他の型を返すクエリを呼び出した</span><span class="sxs-lookup"><span data-stu-id="6162f-268">Called a query to return other types</span></span>
> * <span data-ttu-id="6162f-269">更新クエリを呼び出した</span><span class="sxs-lookup"><span data-stu-id="6162f-269">Called an update query</span></span>
> * <span data-ttu-id="6162f-270">SQL クエリを調べた</span><span class="sxs-lookup"><span data-stu-id="6162f-270">Examined SQL queries</span></span>
> * <span data-ttu-id="6162f-271">抽象化レイヤーを作成した</span><span class="sxs-lookup"><span data-stu-id="6162f-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="6162f-272">変更の自動検出について学習した</span><span class="sxs-lookup"><span data-stu-id="6162f-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="6162f-273">EF Core のソース コードと開発計画について学習した</span><span class="sxs-lookup"><span data-stu-id="6162f-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="6162f-274">動的な LINQ を使ってコードを簡略化する方法を学習した</span><span class="sxs-lookup"><span data-stu-id="6162f-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="6162f-275">これで、ASP.NET Core MVC アプリケーションでの Entity Framework Core の使用に関するチュートリアル シリーズは終了です。</span><span class="sxs-lookup"><span data-stu-id="6162f-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="6162f-276">このシリーズでは新しいデータベースが使用されました。別の方法としては、既存のデータベースからモデルをリバース エンジニアリングします。</span><span class="sxs-lookup"><span data-stu-id="6162f-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6162f-277">チュートリアル: EF Core と MVC、既存のデータベース</span><span class="sxs-lookup"><span data-stu-id="6162f-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
