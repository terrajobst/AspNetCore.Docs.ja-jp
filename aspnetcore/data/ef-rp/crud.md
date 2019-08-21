---
title: ASP.NET Core の Razor ページと EF Core - CRUD - 2/8
author: rick-anderson
description: EF Core で作成、読み取り、更新、削除を行う方法を示します。
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/crud
ms.openlocfilehash: 8dad964826fbf020d250eaec1dbf2845d356ae91
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914768"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a><span data-ttu-id="b0340-103">ASP.NET Core の Razor ページと EF Core - CRUD - 2/8</span><span class="sxs-lookup"><span data-stu-id="b0340-103">Razor Pages with EF Core in ASP.NET Core - CRUD - 2 of 8</span></span>

<span data-ttu-id="b0340-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b0340-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0340-105">このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。</span><span class="sxs-lookup"><span data-stu-id="b0340-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="b0340-106">リポジトリがない</span><span class="sxs-lookup"><span data-stu-id="b0340-106">No repository</span></span>

<span data-ttu-id="b0340-107">一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor Pages) とデータ アクセス層との間に抽象化レイヤーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="b0340-107">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span> <span data-ttu-id="b0340-108">このチュートリアルでは、これは行いません。</span><span class="sxs-lookup"><span data-stu-id="b0340-108">This tutorial doesn't do that.</span></span> <span data-ttu-id="b0340-109">複雑さを最小限に抑え、チュートリアルの焦点を EF Core に置くために、EF Core コードがページ モデル クラスに直接追加されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-109">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="b0340-110">Details ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-110">Update the Details page</span></span>

<span data-ttu-id="b0340-111">Students ページのスキャフォールディング コードには、登録データが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="b0340-111">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="b0340-112">このセクションでは、Details ページに登録を追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-112">In this section, you add enrollments to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="b0340-113">登録の読み取り</span><span class="sxs-lookup"><span data-stu-id="b0340-113">Read enrollments</span></span>

<span data-ttu-id="b0340-114">学生の登録データをページに表示するには、そのデータを読み取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-114">To display a student's enrollment data on the page, you need to read it.</span></span> <span data-ttu-id="b0340-115">*Pages/Students/Details.cshtml.cs* のスキャフォールディング コードでは、Enrollment データを含まない Student データのみが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="b0340-115">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="b0340-116">`OnGetAsync` メソッドを次のコードに置き換えて、選択した学生の登録データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b0340-116">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="b0340-117">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-117">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="b0340-118">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="b0340-118">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="b0340-119">これらのメソッドは、[関連データの読み込み](xref:data/ef-rp/read-related-data)のチュートリアルで詳しく検討します。</span><span class="sxs-lookup"><span data-stu-id="b0340-119">These methods are examined in detail in the [Reading related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="b0340-120">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されないシナリオでパフォーマンスを改善します。</span><span class="sxs-lookup"><span data-stu-id="b0340-120">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="b0340-121">`AsNoTracking` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="b0340-121">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="b0340-122">登録の表示</span><span class="sxs-lookup"><span data-stu-id="b0340-122">Display enrollments</span></span>

<span data-ttu-id="b0340-123">*Pages/Students/Details.cshtml* 内のコードを次のコードに置き換えて、登録のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="b0340-123">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="b0340-124">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-124">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="b0340-125">上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。</span><span class="sxs-lookup"><span data-stu-id="b0340-125">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="b0340-126">登録ごとに、コースのタイトルとグレードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-126">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="b0340-127">コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-127">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="b0340-128">アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b0340-128">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="b0340-129">選択した受講者のコースとグレードの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-129">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="b0340-130">1 つのエンティティを読み取る方法</span><span class="sxs-lookup"><span data-stu-id="b0340-130">Ways to read one entity</span></span>

<span data-ttu-id="b0340-131">生成されたコードでは、[FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用して 1 つのエンティティを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b0340-131">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="b0340-132">このメソッドでは、何も見つからない場合は null が返されます。それ以外の場合は、クエリのフィルター条件を満たす最初の行が返されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-132">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="b0340-133">`FirstOrDefaultAsync` は、通常、次の代替手段よりも適しています。</span><span class="sxs-lookup"><span data-stu-id="b0340-133">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="b0340-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - クエリ フィルターを満たす複数のエンティティがある場合に、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="b0340-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="b0340-135">クエリによって複数の行が返される可能性があるかどうかを判断するため、`SingleOrDefaultAsync` は複数の行をフェッチしようとします。</span><span class="sxs-lookup"><span data-stu-id="b0340-135">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="b0340-136">一意のキーを検索する場合と同様に、クエリが 1 つのエンティティだけを返すことができる場合は、この追加作業は不要です。</span><span class="sxs-lookup"><span data-stu-id="b0340-136">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="b0340-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 主キー (PK) を持つエンティティを検索します。</span><span class="sxs-lookup"><span data-stu-id="b0340-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="b0340-138">PK を持つエンティティがコンテキストによって追跡されている場合、データベースに対する要求がなくても該当するエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-138">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="b0340-139">このメソッドは、単一のエンティティを検索するように最適化されていますが、`FindAsync` を使用して `Include` を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="b0340-139">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="b0340-140">したがって、関連データが必要な場合は、`FirstOrDefaultAsync` を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b0340-140">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="b0340-141">ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="b0340-141">Route data vs. query string</span></span>

<span data-ttu-id="b0340-142">Details ページの URL は `https://localhost:<port>/Students/Details?id=1` です。</span><span class="sxs-lookup"><span data-stu-id="b0340-142">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="b0340-143">エンティティの主キー値がクエリ文字列に含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-143">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="b0340-144">ルート データのキー値を渡すことを好む開発者もいます。`https://localhost:<port>/Students/Details/1`</span><span class="sxs-lookup"><span data-stu-id="b0340-144">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="b0340-145">詳細については、「[生成されたコードの更新](xref:tutorials/razor-pages/da1#update-the-generated-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0340-145">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="b0340-146">Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-146">Update the Create page</span></span>

<span data-ttu-id="b0340-147">Create ページのスキャフォールディングされた `OnPostAsync` コードは、[過剰ポスティング](#overposting)に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="b0340-147">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="b0340-148">*Pages/Students/Create.cshtml.cs* の `OnPostAsync` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b0340-148">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="b0340-149">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="b0340-149">TryUpdateModelAsync</span></span>

<span data-ttu-id="b0340-150">上記のコードでは、Student オブジェクトを作成し、ポストされたフォーム フィールドを使用して Student オブジェクトのプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="b0340-150">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="b0340-151">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) メソッド:</span><span class="sxs-lookup"><span data-stu-id="b0340-151">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="b0340-152">[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからポストされたフォーム値を使用します。</span><span class="sxs-lookup"><span data-stu-id="b0340-152">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="b0340-153">リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="b0340-153">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="b0340-154">"student" のプレフィックスを持つフォーム フィールドを検索します。</span><span class="sxs-lookup"><span data-stu-id="b0340-154">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="b0340-155">たとえば、`Student.FirstMidName` のようにします。</span><span class="sxs-lookup"><span data-stu-id="b0340-155">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="b0340-156">大文字と小文字の区別はありません。</span><span class="sxs-lookup"><span data-stu-id="b0340-156">It's not case sensitive.</span></span>
* <span data-ttu-id="b0340-157">[モデル バインド](xref:mvc/models/model-binding) システムを使用して、文字列からフォーム値を `Student` モデル内の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="b0340-157">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="b0340-158">たとえば、`EnrollmentDate` は DateTime に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-158">For example, `EnrollmentDate` has to be converted to DateTime.</span></span>

<span data-ttu-id="b0340-159">アプリを実行し、Create ページをテストする student エンティティを作成します。</span><span class="sxs-lookup"><span data-stu-id="b0340-159">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="b0340-160">過剰ポスティング</span><span class="sxs-lookup"><span data-stu-id="b0340-160">Overposting</span></span>

<span data-ttu-id="b0340-161">ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。</span><span class="sxs-lookup"><span data-stu-id="b0340-161">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="b0340-162">たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-162">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="b0340-163">アプリの作成または更新の Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` を設定することが可能です。</span><span class="sxs-lookup"><span data-stu-id="b0340-163">Even if the app doesn't have a `Secret` field on the create or update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="b0340-164">ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。</span><span class="sxs-lookup"><span data-stu-id="b0340-164">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="b0340-165">元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。</span><span class="sxs-lookup"><span data-stu-id="b0340-165">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="b0340-166">`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、データベース内で更新されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-166">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="b0340-167">次の図では、Fiddler ツールを使用して、ポストされたフォームの値に `Secret` フィールド (値 "OverPost" を含む) が追加されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-167">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="b0340-169">挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-169">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="b0340-170">これは、アプリ デザイナーで、Create ページで `Secret` プロパティが設定されることを想定していない場合でも発生します。</span><span class="sxs-lookup"><span data-stu-id="b0340-170">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="b0340-171">ビュー モデル</span><span class="sxs-lookup"><span data-stu-id="b0340-171">View model</span></span>

<span data-ttu-id="b0340-172">ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="b0340-172">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="b0340-173">アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b0340-173">The application model is often called the domain model.</span></span> <span data-ttu-id="b0340-174">ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-174">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="b0340-175">ビュー モデルには、使用する UI に必要なプロパティのみが含まれています (たとえば、Create ページ)。</span><span class="sxs-lookup"><span data-stu-id="b0340-175">The view model contains only the properties needed for the UI that it is used for (for example, the Create page).</span></span>

<span data-ttu-id="b0340-176">一部のアプリでは、ビュー モデルに加えて、Razor ページのページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。</span><span class="sxs-lookup"><span data-stu-id="b0340-176">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> 

<span data-ttu-id="b0340-177">次の `Student` ビュー モデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="b0340-177">Consider the following `Student` view model:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

<span data-ttu-id="b0340-178">次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。</span><span class="sxs-lookup"><span data-stu-id="b0340-178">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="b0340-179">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b0340-179">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="b0340-180">`SetValues` では一致するプロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-180">`SetValues` uses property name matching.</span></span> <span data-ttu-id="b0340-181">ビュー モデルの型はモデルの型に関連している必要はなく、プロパティが一致している必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="b0340-181">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="b0340-182">`StudentVM` を使用するには、`Student` ではなく `StudentVM` を使用するように [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-182">Using `StudentVM` requires [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="b0340-183">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-183">Update the Edit page</span></span>

<span data-ttu-id="b0340-184">*Pages/Students/Edit.cshtml.cs* で、`OnGetAsync` メソッドと `OnPostAsync` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b0340-184">In *Pages/Students/Edit.cshtml.cs*, replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="b0340-185">コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-185">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="b0340-186">`FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-186">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="b0340-187">関連データを含める必要がない場合は、`FindAsync` の方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="b0340-187">When you don't have to include related data, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="b0340-188">`OnPostAsync` には `id` パラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="b0340-188">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="b0340-189">現在の学生は、空の学生を作成するのではなく、データベースからフェッチされます。</span><span class="sxs-lookup"><span data-stu-id="b0340-189">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="b0340-190">アプリを実行し、学生を作成して編集することでアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="b0340-190">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="b0340-191">エンティティの状態</span><span class="sxs-lookup"><span data-stu-id="b0340-191">Entity States</span></span>

<span data-ttu-id="b0340-192">データベース コンテキストは、メモリ内のエンティティが、データベース内の対応する行と同期状態にあるかどうかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="b0340-192">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="b0340-193">この追跡情報により、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。</span><span class="sxs-lookup"><span data-stu-id="b0340-193">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="b0340-194">たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-194">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="b0340-195">`SaveChangesAsync` が呼び出されると、データベース コンテキストは SQL の INSERT コマンドを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-195">When `SaveChangesAsync` is called, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="b0340-196">エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-196">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="b0340-197">`Added`:エンティティはデータベースにまだ存在しません。</span><span class="sxs-lookup"><span data-stu-id="b0340-197">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="b0340-198">`SaveChanges` メソッドは INSERT ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-198">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="b0340-199">`Unchanged`:このエンティティでは変更を保存する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="b0340-199">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="b0340-200">エンティティがこの状態になるのは、エンティティがデータベースから読み取られた場合です。</span><span class="sxs-lookup"><span data-stu-id="b0340-200">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="b0340-201">`Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-201">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="b0340-202">`SaveChanges` メソッドは UPDATE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-202">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="b0340-203">`Deleted`:エンティティには削除のマークが付けられています。</span><span class="sxs-lookup"><span data-stu-id="b0340-203">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="b0340-204">`SaveChanges` メソッドは DELETE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-204">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="b0340-205">`Detached`:エンティティはデータベース コンテキストによって追跡されていません。</span><span class="sxs-lookup"><span data-stu-id="b0340-205">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="b0340-206">デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-206">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="b0340-207">エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-207">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="b0340-208">`SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL UPDATE ステートメントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-208">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="b0340-209">Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-209">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="b0340-210">ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-210">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="b0340-211">その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="b0340-211">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="b0340-212">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-212">Update the Delete page</span></span>

<span data-ttu-id="b0340-213">このセクションでは、`SaveChanges` の呼び出しが失敗したときにカスタム エラー メッセージを実装します。</span><span class="sxs-lookup"><span data-stu-id="b0340-213">In this section, you implement a custom error message when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="b0340-214">*Pages/Students/Delete.cshtml.cs* のコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b0340-214">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="b0340-215">変更が強調表示されます (`using` ステートメントのクリーンアップ以外)。</span><span class="sxs-lookup"><span data-stu-id="b0340-215">The changes are highlighted (other than cleanup of `using` statements).</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

<span data-ttu-id="b0340-216">上記のコードでは、省略可能なパラメーター `saveChangesError` を `OnGetAsync` メソッド シグネチャに追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-216">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="b0340-217">`saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="b0340-217">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="b0340-218">一時的なネットワークの問題により、削除操作が失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-218">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="b0340-219">データベースがクラウド内にある場合は、一時的なネットワーク エラーが発生する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="b0340-219">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="b0340-220">Delete ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` パラメーターは false です。</span><span class="sxs-lookup"><span data-stu-id="b0340-220">The `saveChangesError` parameter is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="b0340-221">`OnPostAsync` によって `OnGetAsync` が呼び出された場合 (削除操作が失敗したため)、`saveChangesError` パラメーターは true です。</span><span class="sxs-lookup"><span data-stu-id="b0340-221">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

<span data-ttu-id="b0340-222">`OnPostAsync` メソッドは、選択されたエンティティを取得し、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出して、エンティティの状態を `Deleted` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b0340-222">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="b0340-223">`SaveChanges` が呼び出されると、SQL DELETE コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-223">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="b0340-224">`Remove` が失敗した場合:</span><span class="sxs-lookup"><span data-stu-id="b0340-224">If `Remove` fails:</span></span>

* <span data-ttu-id="b0340-225">データベース例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="b0340-225">The database exception is caught.</span></span>
* <span data-ttu-id="b0340-226">[削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-226">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="b0340-227">Delete Razor ページ (*Pages/Students/Delete.cshtml*) にエラー メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-227">Add an error message to the Delete Razor Page (*Pages/Students/Delete.cshtml*):</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="b0340-228">アプリを実行し、学生を削除して、Delete ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="b0340-228">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b0340-229">次の手順</span><span class="sxs-lookup"><span data-stu-id="b0340-229">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b0340-230">[前のチュートリアル](xref:data/ef-rp/intro)
> [次のチュートリアル](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="b0340-230">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0340-231">このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。</span><span class="sxs-lookup"><span data-stu-id="b0340-231">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="b0340-232">複雑さを最小限に抑え、これらのチュートリアルを通して主眼を EF Core に置くために、EF Core コードはページ モデルで使用されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-232">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="b0340-233">一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor ページ) とデータ アクセス層との間に抽象化レイヤーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="b0340-233">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="b0340-234">このチュートリアルでは、*Student* フォルダー内の [作成]、[編集]、[削除]、[詳細] の各 Razor Pages を確認します。</span><span class="sxs-lookup"><span data-stu-id="b0340-234">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="b0340-235">スキャフォールディング コードでは、[作成]、[編集]、[削除] ページに対して次のパターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="b0340-235">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="b0340-236">要求されたデータを HTTP GET メソッド `OnGetAsync` を使用して取得および表示します。</span><span class="sxs-lookup"><span data-stu-id="b0340-236">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="b0340-237">データに加えた変更を HTTP POST メソッド `OnPostAsync` を使用して保存します。</span><span class="sxs-lookup"><span data-stu-id="b0340-237">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="b0340-238">[インデックス] および [詳細] ページでは、要求されたデータを HTTP GET メソッド `OnGetAsync` を使用して取得および表示します。</span><span class="sxs-lookup"><span data-stu-id="b0340-238">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="b0340-239">SingleOrDefaultAsync と FirstOrDefaultAsync の比較</span><span class="sxs-lookup"><span data-stu-id="b0340-239">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="b0340-240">生成されたコードでは [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用しています。これは一般に [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) よりも好まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-240">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="b0340-241">`FirstOrDefaultAsync` は、1 つのエンティティをフェッチする際に `SingleOrDefaultAsync` よりも効率的です。</span><span class="sxs-lookup"><span data-stu-id="b0340-241">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="b0340-242">クエリから返されるエンティティが 1 つ以下であることを、コードで検証する必要がない。</span><span class="sxs-lookup"><span data-stu-id="b0340-242">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="b0340-243">`SingleOrDefaultAsync` がより多くのデータをフェッチし、不要な作業を行う。</span><span class="sxs-lookup"><span data-stu-id="b0340-243">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="b0340-244">フィルター部に適合するエンティティが複数存在する場合に `SingleOrDefaultAsync` によって例外がスローされる。</span><span class="sxs-lookup"><span data-stu-id="b0340-244">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="b0340-245">フィルター部に適合するエンティティが複数存在する場合に `FirstOrDefaultAsync` によって例外がスローされない。</span><span class="sxs-lookup"><span data-stu-id="b0340-245">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="b0340-246">FindAsync</span><span class="sxs-lookup"><span data-stu-id="b0340-246">FindAsync</span></span>

<span data-ttu-id="b0340-247">スキャフォールディング コードの多くでは、`FirstOrDefaultAsync` ではなく、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="b0340-247">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="b0340-248">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="b0340-248">`FindAsync`:</span></span>

* <span data-ttu-id="b0340-249">主キー (PK) を持つエンティティを検索します。</span><span class="sxs-lookup"><span data-stu-id="b0340-249">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="b0340-250">PK を持つエンティティがコンテキストによって追跡されている場合、DB に対する要求がなくても該当するエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-250">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="b0340-251">単純で簡潔です。</span><span class="sxs-lookup"><span data-stu-id="b0340-251">Is simple and concise.</span></span>
* <span data-ttu-id="b0340-252">単一のエンティティを検索するように最適化されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-252">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="b0340-253">状況によってはパフォーマンス上の利点がありますが、通常の Web アプリではほとんど利点がありません。</span><span class="sxs-lookup"><span data-stu-id="b0340-253">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="b0340-254">[SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) ではなく、[FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) を暗黙的に使用します。</span><span class="sxs-lookup"><span data-stu-id="b0340-254">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="b0340-255">ただし、`Include` で他のエンティティを含める場合、`FindAsync` は適切ではなくなります。</span><span class="sxs-lookup"><span data-stu-id="b0340-255">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="b0340-256">つまり、アプリの進行状況に応じて、`FindAsync` を止めてクエリに移行することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-256">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="b0340-257">Details ページをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="b0340-257">Customize the Details page</span></span>

<span data-ttu-id="b0340-258">`Pages/Students` ページを参照します。</span><span class="sxs-lookup"><span data-stu-id="b0340-258">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="b0340-259">**[編集]** 、 **[詳細]** 、 **[削除]** の各リンクは、*Pages/Students/Index.cshtml* ファイルで[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-259">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="b0340-260">アプリを実行し、 **[詳細]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="b0340-260">Run the app and select a **Details** link.</span></span> <span data-ttu-id="b0340-261">URL の形式は、 `http://localhost:5000/Students/Details?id=2` です。</span><span class="sxs-lookup"><span data-stu-id="b0340-261">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="b0340-262">クエリ文字列 (`?id=2`) によって受講者 ID が渡されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-262">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="b0340-263">`"{id:int}"` ルート テンプレートを使用するには、[編集]、[詳細]、[削除] の Razor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="b0340-263">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="b0340-264">これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b0340-264">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="b0340-265">整数ルート値を**含まない**、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-265">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="b0340-266">たとえば、 `http://localhost:5000/Students/Details` は 404 エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="b0340-266">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="b0340-267">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-267">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="b0340-268">アプリを実行し、[詳細] リンクをクリックし、URL がルート データとして ID を渡していることを確認します ( `http://localhost:5000/Students/Details/2` )。</span><span class="sxs-lookup"><span data-stu-id="b0340-268">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="b0340-269">`@page` から `@page "{id:int}"` への変更をグローバルに行わないでください。これを行うと、[ホーム] ページと [作成] ページへのリンクが解除されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-269">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="b0340-270">関連データを追加する</span><span class="sxs-lookup"><span data-stu-id="b0340-270">Add related data</span></span>

<span data-ttu-id="b0340-271">Students インデックス ページのスキャフォールディング コードには、`Enrollments` プロパティが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="b0340-271">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="b0340-272">このセクションでは、`Enrollments` コレクションのコンテンツが [詳細] ページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-272">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="b0340-273">*Pages/Students/Details.cshtml.cs* の `OnGetAsync` メソッドでは、`FirstOrDefaultAsync` メソッドを使用して単一の `Student` エンティティを取得します。</span><span class="sxs-lookup"><span data-stu-id="b0340-273">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="b0340-274">次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-274">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="b0340-275">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="b0340-275">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="b0340-276">これらのメソッドは、データの読み取りに関連するチュートリアルで詳しく検討します。</span><span class="sxs-lookup"><span data-stu-id="b0340-276">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="b0340-277">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されない場合のシナリオでパフォーマンスを改善します。</span><span class="sxs-lookup"><span data-stu-id="b0340-277">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="b0340-278">`AsNoTracking` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="b0340-278">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="b0340-279">[詳細] ページに関連する登録を表示する</span><span class="sxs-lookup"><span data-stu-id="b0340-279">Display related enrollments on the Details page</span></span>

<span data-ttu-id="b0340-280">*Pages/Students/Details.cshtml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="b0340-280">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="b0340-281">登録の一覧を表示するために、次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-281">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="b0340-282">コードを貼り付けた後でコードのインデントが乱れた場合は、CTRL + D + K キーを押してそれを修正します。</span><span class="sxs-lookup"><span data-stu-id="b0340-282">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="b0340-283">上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。</span><span class="sxs-lookup"><span data-stu-id="b0340-283">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="b0340-284">登録ごとに、コースのタイトルとグレードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-284">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="b0340-285">コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-285">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="b0340-286">アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b0340-286">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="b0340-287">選択した受講者のコースとグレードの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-287">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="b0340-288">[作成] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-288">Update the Create page</span></span>

<span data-ttu-id="b0340-289">次のコードを使用して、*Pages/Students/Create.cshtml.cs* 内の `OnPostAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="b0340-289">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="b0340-290">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="b0340-290">TryUpdateModelAsync</span></span>

<span data-ttu-id="b0340-291">次の [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) コードを検討します。</span><span class="sxs-lookup"><span data-stu-id="b0340-291">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="b0340-292">上記のコードで、`TryUpdateModelAsync<Student>` は、[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからのポストされたフォームの値を使用して `emptyStudent` オブジェクトの更新を試みます。</span><span class="sxs-lookup"><span data-stu-id="b0340-292">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="b0340-293">`TryUpdateModelAsync` は、リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="b0340-293">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="b0340-294">上記のサンプルについて:</span><span class="sxs-lookup"><span data-stu-id="b0340-294">In the preceding sample:</span></span>

* <span data-ttu-id="b0340-295">2 番目の引数 (`"student", // Prefix`) には、値を検索するためのプレフィックスが使用されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-295">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="b0340-296">大文字と小文字の区別はありません。</span><span class="sxs-lookup"><span data-stu-id="b0340-296">It's not case sensitive.</span></span>
* <span data-ttu-id="b0340-297">ポストされたフォームの値は、[モデル バインディング](xref:mvc/models/model-binding) を使用して `Student` モデル内の型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-297">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="b0340-298">過剰ポスティング</span><span class="sxs-lookup"><span data-stu-id="b0340-298">Overposting</span></span>

<span data-ttu-id="b0340-299">ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。</span><span class="sxs-lookup"><span data-stu-id="b0340-299">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="b0340-300">たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-300">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="b0340-301">アプリの [作成]/[更新] Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` を設定することが可能です。</span><span class="sxs-lookup"><span data-stu-id="b0340-301">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="b0340-302">ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。</span><span class="sxs-lookup"><span data-stu-id="b0340-302">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="b0340-303">元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。</span><span class="sxs-lookup"><span data-stu-id="b0340-303">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="b0340-304">`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、DB 内で更新されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-304">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="b0340-305">次の図では、Fiddler ツールを使用して、ポストされたフォームの値に `Secret` フィールド (値 "OverPost" を含む) が追加されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-305">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="b0340-307">挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-307">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="b0340-308">アプリ デザイナーは、[作成] ページで `Secret` プロパティが設定されることを想定していませんでした。</span><span class="sxs-lookup"><span data-stu-id="b0340-308">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="b0340-309">ビュー モデル</span><span class="sxs-lookup"><span data-stu-id="b0340-309">View model</span></span>

<span data-ttu-id="b0340-310">ビュー モデルには、通常、アプリケーションで使用されるモデルに含まれるプロパティのサブセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-310">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="b0340-311">アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b0340-311">The application model is often called the domain model.</span></span> <span data-ttu-id="b0340-312">ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-312">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="b0340-313">ビュー モデルには、UI レイヤーで必要なプロパティのみが含まれています (たとえば、[作成] ページ)。</span><span class="sxs-lookup"><span data-stu-id="b0340-313">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="b0340-314">一部のアプリでは、ビュー モデルに加えて、Razor ページのページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。</span><span class="sxs-lookup"><span data-stu-id="b0340-314">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="b0340-315">次の `Student` ビュー モデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="b0340-315">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="b0340-316">ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="b0340-316">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="b0340-317">ビュー モデルには、表示または更新されるプロパティのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-317">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="b0340-318">次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。</span><span class="sxs-lookup"><span data-stu-id="b0340-318">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="b0340-319">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b0340-319">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="b0340-320">`SetValues` では一致するプロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-320">`SetValues` uses property name matching.</span></span> <span data-ttu-id="b0340-321">ビュー モデルの型はモデルの型に関連している必要はなく、プロパティが一致している必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="b0340-321">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="b0340-322">`StudentVM` を使用するには、`Student` ではなく `StudentVM` を使用するように [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-322">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="b0340-323">Razor ページで、`PageModel` 派生クラスはビュー モデルです。</span><span class="sxs-lookup"><span data-stu-id="b0340-323">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="b0340-324">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-324">Update the Edit page</span></span>

<span data-ttu-id="b0340-325">[編集] ページのページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="b0340-325">Update the page model for the Edit page.</span></span> <span data-ttu-id="b0340-326">大きな変更点が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-326">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="b0340-327">コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-327">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="b0340-328">`OnPostAsync` には省略可能な `id` パラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="b0340-328">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="b0340-329">現在の受講者は、空の受講者を作成するのではなく、DB からフェッチされます。</span><span class="sxs-lookup"><span data-stu-id="b0340-329">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="b0340-330">`FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-330">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="b0340-331">主キーからエンティティを選択する場合は、`FindAsync` をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b0340-331">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="b0340-332">詳細については、「[FindAsync](#FindAsync)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0340-332">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="b0340-333">[編集] ページと [作成] ページをテストする</span><span class="sxs-lookup"><span data-stu-id="b0340-333">Test the Edit and Create pages</span></span>

<span data-ttu-id="b0340-334">いくつかの受講者エンティティを作成して編集します。</span><span class="sxs-lookup"><span data-stu-id="b0340-334">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="b0340-335">エンティティの状態</span><span class="sxs-lookup"><span data-stu-id="b0340-335">Entity States</span></span>

<span data-ttu-id="b0340-336">DB コンテキストは、メモリ内のエンティティが、DB 内の対応する行と同期状態にあるかどうかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="b0340-336">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="b0340-337">DB コンテキストの同期情報から、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。</span><span class="sxs-lookup"><span data-stu-id="b0340-337">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="b0340-338">たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-338">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="b0340-339">`SaveChangesAsync` が呼び出されると、DB コンテキストは SQL の INSERT コマンドを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-339">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="b0340-340">エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-340">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="b0340-341">`Added`:エンティティは DB にまだ存在しません。</span><span class="sxs-lookup"><span data-stu-id="b0340-341">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="b0340-342">`SaveChanges` メソッドは INSERT ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-342">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="b0340-343">`Unchanged`:このエンティティでは変更を保存する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="b0340-343">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="b0340-344">エンティティがこの状態になるのは、エンティティが DB から読み取られた場合です。</span><span class="sxs-lookup"><span data-stu-id="b0340-344">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="b0340-345">`Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。</span><span class="sxs-lookup"><span data-stu-id="b0340-345">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="b0340-346">`SaveChanges` メソッドは UPDATE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-346">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="b0340-347">`Deleted`:エンティティには削除のマークが付けられています。</span><span class="sxs-lookup"><span data-stu-id="b0340-347">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="b0340-348">`SaveChanges` メソッドは DELETE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b0340-348">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="b0340-349">`Detached`:エンティティは DB コンテキストによって追跡されていません。</span><span class="sxs-lookup"><span data-stu-id="b0340-349">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="b0340-350">デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-350">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="b0340-351">エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-351">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="b0340-352">`SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL UPDATE ステートメントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-352">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="b0340-353">Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-353">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="b0340-354">ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-354">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="b0340-355">その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="b0340-355">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="b0340-356">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-356">Update the Delete page</span></span>

<span data-ttu-id="b0340-357">このセクションでは、`SaveChanges` の呼び出しが失敗したときにカスタム エラー メッセージを実装するためのコードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-357">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="b0340-358">考えられるエラー メッセージを含められるように文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-358">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="b0340-359">`OnGetAsync` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b0340-359">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="b0340-360">上記のコードには、省略可能なパラメーター `saveChangesError` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0340-360">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="b0340-361">`saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="b0340-361">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="b0340-362">一時的なネットワークの問題により、削除操作が失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-362">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="b0340-363">一時的なネットワーク エラーが高い可能性でクラウド内に発生しています。</span><span class="sxs-lookup"><span data-stu-id="b0340-363">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="b0340-364">[削除] ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` は false です。</span><span class="sxs-lookup"><span data-stu-id="b0340-364">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="b0340-365">`OnPostAsync` によって `OnGetAsync` が呼び出された場合 (削除操作が失敗したため)、`saveChangesError` パラメーターは true です。</span><span class="sxs-lookup"><span data-stu-id="b0340-365">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="b0340-366">[削除] ページの OnPostAsync メソッド</span><span class="sxs-lookup"><span data-stu-id="b0340-366">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="b0340-367">`OnPostAsync` を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b0340-367">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="b0340-368">上記のコードでは、選択されたエンティティを取得してから、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出してエンティティの状態を `Deleted` に設定しています。</span><span class="sxs-lookup"><span data-stu-id="b0340-368">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="b0340-369">`SaveChanges` が呼び出されると、SQL DELETE コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-369">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="b0340-370">`Remove` が失敗した場合:</span><span class="sxs-lookup"><span data-stu-id="b0340-370">If `Remove` fails:</span></span>

* <span data-ttu-id="b0340-371">DB 例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="b0340-371">The DB exception is caught.</span></span>
* <span data-ttu-id="b0340-372">[削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b0340-372">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="b0340-373">[削除] Razor ページを更新する</span><span class="sxs-lookup"><span data-stu-id="b0340-373">Update the Delete Razor Page</span></span>

<span data-ttu-id="b0340-374">次の強調表示されたエラー メッセージを [削除] Razor ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="b0340-374">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="b0340-375">[削除] をテストします。</span><span class="sxs-lookup"><span data-stu-id="b0340-375">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="b0340-376">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="b0340-376">Common errors</span></span>

<span data-ttu-id="b0340-377">[受講者]/[インデックス] またはその他のリンクが機能しません。</span><span class="sxs-lookup"><span data-stu-id="b0340-377">Students/Index or other links don't work:</span></span>

<span data-ttu-id="b0340-378">Razor ページに正しい `@page` ディレクティブが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b0340-378">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="b0340-379">たとえば、[受講者]/[インデックス] Razor ページにルート テンプレートを含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="b0340-379">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="b0340-380">各 Razor ページには、`@page` ディレクティブを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0340-380">Each Razor Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="b0340-381">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b0340-381">Additional resources</span></span>

* [<span data-ttu-id="b0340-382">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="b0340-382">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="b0340-383">[前へ](xref:data/ef-rp/intro)
> [次へ](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="b0340-383">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end
