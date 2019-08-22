---
title: 'チュートリアル: CRUD 機能を実装する - ASP.NET MVC と EF Core'
description: このチュートリアルでは、MVC スキャフォールディングがコントローラーとビュー用に自動的に作成する CRUD (作成、読み取り、更新、削除) コードを確認およびカスタマイズします。
author: tdykstra
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/crud
ms.openlocfilehash: 975738200f9ea621c38b395be123b1d05c78298e
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886303"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a><span data-ttu-id="7e4b0-103">チュートリアル: CRUD 機能を実装する - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="7e4b0-103">Tutorial: Implement CRUD Functionality - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="7e4b0-104">前のチュートリアルでは、Entity Framework と SQL Server LocalDB を使ってデータを保存して表示する MVC アプリケーションを作成しました。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-104">In the previous tutorial, you created an MVC application that stores and displays data using the Entity Framework and SQL Server LocalDB.</span></span> <span data-ttu-id="7e4b0-105">このチュートリアルでは、MVC スキャフォールディングがコントローラーとビュー用に自動的に作成する CRUD (作成、読み取り、更新、削除) コードを確認およびカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-105">In this tutorial, you'll review and customize the CRUD (create, read, update, delete) code that the MVC scaffolding automatically creates for you in controllers and views.</span></span>

> [!NOTE]
> <span data-ttu-id="7e4b0-106">コントローラーとデータ アクセス層の間に抽象化レイヤーを作成するためにリポジトリ パターンを実装することは、よく行われることです。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-106">It's a common practice to implement the repository pattern in order to create an abstraction layer between your controller and the data access layer.</span></span> <span data-ttu-id="7e4b0-107">この一連のチュートリアルが複雑にならないようにし、Entity Framework 自体の使い方に集中できるように、チュートリアルではリポジトリは使われていません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-107">To keep these tutorials simple and focused on teaching how to use the Entity Framework itself, they don't use repositories.</span></span> <span data-ttu-id="7e4b0-108">EF でのリポジトリについては、[このシリーズの最後のチュートリアル](advanced.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-108">For information about repositories with EF, see [the last tutorial in this series](advanced.md).</span></span>

<span data-ttu-id="7e4b0-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7e4b0-110">Details ページをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="7e4b0-110">Customize the Details page</span></span>
> * <span data-ttu-id="7e4b0-111">Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-111">Update the Create page</span></span>
> * <span data-ttu-id="7e4b0-112">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-112">Update the Edit page</span></span>
> * <span data-ttu-id="7e4b0-113">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-113">Update the Delete page</span></span>
> * <span data-ttu-id="7e4b0-114">データベース接続を閉じる</span><span class="sxs-lookup"><span data-stu-id="7e4b0-114">Close database connections</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e4b0-115">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7e4b0-115">Prerequisites</span></span>

* [<span data-ttu-id="7e4b0-116">EF Core と ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="7e4b0-116">Get started with EF Core and ASP.NET Core MVC</span></span>](intro.md)

## <a name="customize-the-details-page"></a><span data-ttu-id="7e4b0-117">Details ページをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="7e4b0-117">Customize the Details page</span></span>

<span data-ttu-id="7e4b0-118">Students/Index ページのスキャフォールディングされたコードでは、`Enrollments` プロパティが省略されています。これは、このプロパティがコレクションを保持しているためです。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-118">The scaffolded code for the Students Index page left out the `Enrollments` property, because that property holds a collection.</span></span> <span data-ttu-id="7e4b0-119">**Details** ページでは、コレクションの内容を HTML テーブルで表示します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-119">In the **Details** page, you'll display the contents of the collection in an HTML table.</span></span>

<span data-ttu-id="7e4b0-120">*Controllers/StudentsController.cs* に含まれる Details ビューのアクション メソッドでは、`SingleOrDefaultAsync` メソッドを使って 1 つの `Student` エンティティを取得しています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-120">In *Controllers/StudentsController.cs*, the action method for the Details view uses the `SingleOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="7e4b0-121">`Include`、</span><span class="sxs-lookup"><span data-stu-id="7e4b0-121">Add code that calls `Include`.</span></span> <span data-ttu-id="7e4b0-122">`ThenInclude`、および `AsNoTracking` メソッドを呼び出すコードを、次の強調表示された部分で示すように追加します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-122">`ThenInclude`,  and `AsNoTracking` methods, as shown in the following highlighted code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="7e4b0-123">`Include` メソッドと `ThenInclude` メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-123">The `Include` and `ThenInclude` methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span>  <span data-ttu-id="7e4b0-124">これらのメソッドについては、[関連データの読み取り](read-related-data.md)チュートリアルをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-124">You'll learn more about these methods in the [read related data](read-related-data.md) tutorial.</span></span>

<span data-ttu-id="7e4b0-125">`AsNoTracking` メソッドを使うと、返されたエンティティが現在のコンテキストの有効期間内に更新されないシナリオで、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-125">The `AsNoTracking` method improves performance in scenarios where the entities returned won't be updated in the current context's lifetime.</span></span> <span data-ttu-id="7e4b0-126">詳細については、このチュートリアルの最後の `AsNoTracking` をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-126">You'll learn more about `AsNoTracking` at the end of this tutorial.</span></span>

### <a name="route-data"></a><span data-ttu-id="7e4b0-127">ルート データ</span><span class="sxs-lookup"><span data-stu-id="7e4b0-127">Route data</span></span>

<span data-ttu-id="7e4b0-128">`Details` メソッドに渡すキー値は、"*ルート データ*" から取得します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-128">The key value that's passed to the `Details` method comes from *route data*.</span></span> <span data-ttu-id="7e4b0-129">ルート データは、モデル バインダーが URL のセグメント内で検出したデータです。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-129">Route data is data that the model binder found in a segment of the URL.</span></span> <span data-ttu-id="7e4b0-130">たとえば、既定のルートでは、controller、action、id の各セグメントが指定されています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-130">For example, the default route specifies controller, action, and id segments:</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

<span data-ttu-id="7e4b0-131">次の URL では、既定のルートは、Instructor を controller として、Index を action として、1 を id としてマップします。これらがルート データの値です。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-131">In the following URL, the default route maps Instructor as the controller, Index as the action, and 1 as the id; these are route data values.</span></span>

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

<span data-ttu-id="7e4b0-132">URL の最後の部分 ("?courseID=2021") は、クエリ文字列の値です。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-132">The last part of the URL ("?courseID=2021") is a query string value.</span></span> <span data-ttu-id="7e4b0-133">ID をクエリ文字列の値として渡した場合、モデル バインダーは `Index` メソッドの `id` パラメーターにも ID 値を渡します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-133">The model binder will also pass the ID value to the `Index` method `id` parameter if you pass it as a query string value:</span></span>

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

<span data-ttu-id="7e4b0-134">Index ページでは、Razor ビューのタグ ヘルパーのステートメントによって、ハイパーリンクの URL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-134">In the Index page, hyperlink URLs are created by tag helper statements in the Razor view.</span></span> <span data-ttu-id="7e4b0-135">次の Razor コードでは、`id` パラメーターが既定のルートと一致するため、`id` がルート データに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-135">In the following Razor code, the `id` parameter matches the default route, so `id` is added to the route data.</span></span>

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

<span data-ttu-id="7e4b0-136">これにより、`item.ID` が 6 のときは次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-136">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit/6">Edit</a>
```

<span data-ttu-id="7e4b0-137">次の Razor コードでは、`studentID` は既定ルートのパラメーターと一致しないため、クエリ文字列として追加されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-137">In the following Razor code, `studentID` doesn't match a parameter in the default route, so it's added as a query string.</span></span>

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

<span data-ttu-id="7e4b0-138">これにより、`item.ID` が 6 のときは次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-138">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

<span data-ttu-id="7e4b0-139">タグ ヘルパーの詳細については、「<xref:mvc/views/tag-helpers/intro>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-139">For more information about tag helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

### <a name="add-enrollments-to-the-details-view"></a><span data-ttu-id="7e4b0-140">Details ビューに登録を追加する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-140">Add enrollments to the Details view</span></span>

<span data-ttu-id="7e4b0-141">*Views/Students/Details.cshtml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-141">Open *Views/Students/Details.cshtml*.</span></span> <span data-ttu-id="7e4b0-142">次の例で示すように、`DisplayNameFor` および `DisplayFor` ヘルパーを使って各フィールドが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-142">Each field is displayed using `DisplayNameFor` and `DisplayFor` helpers, as shown in the following example:</span></span>

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

<span data-ttu-id="7e4b0-143">最後のフィールドの後、終了タグ `</dl>` の直前に、登録の一覧を表示する次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-143">After the last field and immediately before the closing `</dl>` tag, add the following code to display a list of enrollments:</span></span>

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

<span data-ttu-id="7e4b0-144">コードを貼り付けた後でコードのインデントが乱れた場合は、Ctrl + D + K キーを押して修正します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-144">If code indentation is wrong after you paste the code, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="7e4b0-145">このコードは、`Enrollments` ナビゲーション プロパティ内のエンティティをループ処理します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-145">This code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="7e4b0-146">登録ごとに、コースのタイトルとグレードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-146">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="7e4b0-147">コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-147">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="7e4b0-148">アプリを実行し、 **[Students]** タブを選んで、受講者の **[Details]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-148">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="7e4b0-149">選んだ受講者のコースとグレードの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-149">You see the list of courses and grades for the selected student:</span></span>

![Student/Details ページ](crud/_static/student-details.png)

## <a name="update-the-create-page"></a><span data-ttu-id="7e4b0-151">Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-151">Update the Create page</span></span>

<span data-ttu-id="7e4b0-152">*StudentsController.cs* で、HttpPost の `Create` メソッドを変更し、try-catch ブロックを追加して、`Bind` 属性から ID を削除します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-152">In *StudentsController.cs*, modify the HttpPost `Create` method by adding a try-catch block and removing ID from the `Bind` attribute.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

<span data-ttu-id="7e4b0-153">このコードは、ASP.NET Core MVC モデル バインダーによって作成された Student エンティティを Students エンティティ セットに追加した後、変更をデータベースに保存します</span><span class="sxs-lookup"><span data-stu-id="7e4b0-153">This code adds the Student entity created by the ASP.NET Core MVC model binder to the Students entity set and then saves the changes to the database.</span></span> <span data-ttu-id="7e4b0-154">(モデル バインダーとは、フォームによって送信されたデータの操作を容易にする ASP.NET Core MVC の機能です。モデル バインダーは、ポストされたフォーム値を CLR 型に変換して、パラメーター内のアクション メソッドに渡します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-154">(Model binder refers to the ASP.NET Core MVC functionality that makes it easier for you to work with data submitted by a form; a model binder converts posted form values to CLR types and passes them to the action method in parameters.</span></span> <span data-ttu-id="7e4b0-155">この例のモデル バインダーは、Form コレクションからのプロパティ値を使って、Student エンティティを自動的にインスタンス化します)。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-155">In this case, the model binder instantiates a Student entity for you using property values from the Form collection.)</span></span>

<span data-ttu-id="7e4b0-156">`ID` は行が挿入されるときに SQL Server によって自動的に設定される主キー値であるため、`Bind` 属性から ID を削除しました。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-156">You removed `ID` from the `Bind` attribute because ID is the primary key value which SQL Server will set automatically when the row is inserted.</span></span> <span data-ttu-id="7e4b0-157">ユーザーからの入力によって ID 値が設定されることはありません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-157">Input from the user doesn't set the ID value.</span></span>

<span data-ttu-id="7e4b0-158">`Bind` 属性以外では、スキャフォールディングされたコードに対して行った変更は try-catch ブロックだけです。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-158">Other than the `Bind` attribute, the try-catch block is the only change you've made to the scaffolded code.</span></span> <span data-ttu-id="7e4b0-159">変更を保存するときに、`DbUpdateException` から派生した例外がキャッチされた場合は、汎用的なエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-159">If an exception that derives from `DbUpdateException` is caught while the changes are being saved, a generic error message is displayed.</span></span> <span data-ttu-id="7e4b0-160">`DbUpdateException` 例外は、プログラミング エラーではなくアプリケーション外の何かが原因で発生する場合があるので、再試行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-160">`DbUpdateException` exceptions are sometimes caused by something external to the application rather than a programming error, so the user is advised to try again.</span></span> <span data-ttu-id="7e4b0-161">このサンプルでは実装されていませんが、運用品質のアプリケーションでは例外をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-161">Although not implemented in this sample, a production quality application would log the exception.</span></span> <span data-ttu-id="7e4b0-162">詳細については、「[Monitoring and Telemetry (Building Real-World Cloud Apps with Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)」(監視とテレメトリ (Azure での実際のクラウド アプリの構築)) の「**Log for insight**」(洞察のためのログ) セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-162">For more information, see the **Log for insight** section in [Monitoring and Telemetry (Building Real-World Cloud Apps with Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).</span></span>

<span data-ttu-id="7e4b0-163">`ValidateAntiForgeryToken` 属性は、クロスサイト リクエスト フォージェリ (CSRF) 攻撃を防ぐのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-163">The `ValidateAntiForgeryToken` attribute helps prevent cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="7e4b0-164">トークンは、[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) によってビューに自動的に挿入され、ユーザーがフォームを送信するときに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-164">The token is automatically injected into the view by the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) and is included when the form is submitted by the user.</span></span> <span data-ttu-id="7e4b0-165">トークンは、`ValidateAntiForgeryToken` 属性によって検証されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-165">The token is validated by the `ValidateAntiForgeryToken` attribute.</span></span> <span data-ttu-id="7e4b0-166">CSRF については、[リクエスト フォージェリの対策](../../security/anti-request-forgery.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-166">For more information about CSRF, see [Anti-Request Forgery](../../security/anti-request-forgery.md).</span></span>

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a><span data-ttu-id="7e4b0-167">過剰ポスティングに関するセキュリティの注意事項</span><span class="sxs-lookup"><span data-stu-id="7e4b0-167">Security note about overposting</span></span>

<span data-ttu-id="7e4b0-168">スキャフォールディングされたコードによって `Create` メソッドに追加される `Bind` 属性は、作成シナリオで過剰ポスティングを防ぐ 1 つの方法です。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-168">The `Bind` attribute that the scaffolded code includes on the `Create` method is one way to protect against overposting in create scenarios.</span></span> <span data-ttu-id="7e4b0-169">たとえば、Student エンティティに、この Web ページで設定したくない `Secret` プロパティが含まれているものとします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-169">For example, suppose the Student entity includes a `Secret` property that you don't want this web page to set.</span></span>

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

<span data-ttu-id="7e4b0-170">Web ページに `Secret` フィールドを作らなくても、ハッカーは、Fiddler などのツールを使うか、何らかの JavaScript を作成して、`Secret` フォーム値をポストすることできます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-170">Even if you don't have a `Secret` field on the web page, a hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="7e4b0-171">モデル バインダーが Student インスタンスを作成するときに使うフィールドを制限する `Bind` 属性がないと、モデル バインダーはその `Secret` フォーム値を取得し、それを使って Student インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-171">Without the `Bind` attribute limiting the fields that the model binder uses when it creates a Student instance, the model binder would pick up that `Secret` form value and use it to create the Student entity instance.</span></span> <span data-ttu-id="7e4b0-172">その場合、`Secret` フォーム フィールドに対してハッカーが指定した値はすべて、データベースで更新されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-172">Then whatever value the hacker specified for the `Secret` form field would be updated in your database.</span></span> <span data-ttu-id="7e4b0-173">次の図では、Fiddler ツールを使用して、ポストされたフォームの値に `Secret` フィールド (値 "OverPost" を含む) が追加されています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-173">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler による Secret フィールドの追加](crud/_static/fiddler.png)

<span data-ttu-id="7e4b0-175">値 "OverPost" は挿入される行の `Secret` プロパティに正常に追加されますが、Web ページがそのプロパティを設定できることは意図したものではありません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-175">The value "OverPost" would then be successfully added to the `Secret` property of the inserted row, although you never intended that the web page be able to set that property.</span></span>

<span data-ttu-id="7e4b0-176">最初にデータベースからエンティティを読み取り、`TryUpdateModel` を呼び出して明示的に許可されたプロパティ リストを渡すことにより、編集シナリオでの過剰ポスティングを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-176">You can prevent overposting in edit scenarios by reading the entity from the database first and then calling `TryUpdateModel`, passing in an explicit allowed properties list.</span></span> <span data-ttu-id="7e4b0-177">これらのチュートリアルではその方法が使われています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-177">That's the method used in these tutorials.</span></span>

<span data-ttu-id="7e4b0-178">多くの開発者に好まれている、過剰ポスティングを防ぐためのもう 1 つの方法は、エンティティ クラスではなくビュー モデルをモデル バインドで使うことです。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-178">An alternative way to prevent overposting that's preferred by many developers is to use view models rather than entity classes with model binding.</span></span> <span data-ttu-id="7e4b0-179">更新するプロパティのみをビュー モデルに含めます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-179">Include only the properties you want to update in the view model.</span></span> <span data-ttu-id="7e4b0-180">MVC モデル バインダーが終了したら、必要に応じて AutoMapper などのツールを使って、ビュー モデルのプロパティをエンティティ インスタンスにコピーします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-180">Once the MVC model binder has finished, copy the view model properties to the entity instance, optionally using a tool such as AutoMapper.</span></span> <span data-ttu-id="7e4b0-181">エンティティのインスタンスで `_context.Entry` を使ってその状態を `Unchanged` に設定した後、ビュー モデルに含まれる各エンティティ プロパティで `Property("PropertyName").IsModified` を true に設定します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-181">Use `_context.Entry` on the entity instance to set its state to `Unchanged`, and then set `Property("PropertyName").IsModified` to true on each entity property that's included in the view model.</span></span> <span data-ttu-id="7e4b0-182">この方法は、編集シナリオと作成シナリオの両方で利用できます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-182">This method works in both edit and create scenarios.</span></span>

### <a name="test-the-create-page"></a><span data-ttu-id="7e4b0-183">Create ページをテストする</span><span class="sxs-lookup"><span data-stu-id="7e4b0-183">Test the Create page</span></span>

<span data-ttu-id="7e4b0-184">*Views/Students/Create.cshtml* 内のコードでは、各フィールドに対して `label`、`input`、`span` (検証メッセージ用) の各タグ ヘルパーが使われています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-184">The code in *Views/Students/Create.cshtml* uses `label`, `input`, and `span` (for validation messages) tag helpers for each field.</span></span>

<span data-ttu-id="7e4b0-185">アプリを実行し、 **[Students]** タブを選んで、 **[Create New]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-185">Run the app, select the **Students** tab, and click **Create New**.</span></span>

<span data-ttu-id="7e4b0-186">名前と日付を入力します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-186">Enter names and a date.</span></span> <span data-ttu-id="7e4b0-187">お使いのブラウザーでできる場合は、無効な日付を入力してみてください</span><span class="sxs-lookup"><span data-stu-id="7e4b0-187">Try entering an invalid date if your browser lets you do that.</span></span> <span data-ttu-id="7e4b0-188">(ブラウザーによっては、日付選択機能が強制的に使われます)。その後、 **[Create]** をクリックしてエラー メッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-188">(Some browsers force you to use a date picker.) Then click **Create** to see the error message.</span></span>

![データ検証エラー](crud/_static/date-error.png)

<span data-ttu-id="7e4b0-190">これは、既定で作成されるサーバー側の検証です。後のチュートリアルでは、クライアント側検証用コードも生成する属性を追加する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-190">This is server-side validation that you get by default; in a later tutorial you'll see how to add attributes that will generate code for client-side validation also.</span></span> <span data-ttu-id="7e4b0-191">次の強調表示されたコードは、`Create` メソッドでのモデル検証チェックの部分です。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-191">The following highlighted code shows the model validation check in the `Create` method.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

<span data-ttu-id="7e4b0-192">日付を有効な値に変更し、 **[Create]** をクリックして、新しい学生が **[Index]** ページに表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-192">Change the date to a valid value and click **Create** to see the new student appear in the **Index** page.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="7e4b0-193">Edit ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-193">Update the Edit page</span></span>

<span data-ttu-id="7e4b0-194">*StudentController.cs* に含まれる HttpGet の `Edit` メソッド (`HttpPost` 属性がないもの) は、`SingleOrDefaultAsync` メソッドを使って、選ばれた Student エンティティを取得します (`Details` メソッドと同様)。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-194">In *StudentController.cs*, the HttpGet `Edit` method (the one without the `HttpPost` attribute) uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the `Details` method.</span></span> <span data-ttu-id="7e4b0-195">このメソッドを変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-195">You don't need to change this method.</span></span>

### <a name="recommended-httppost-edit-code-read-and-update"></a><span data-ttu-id="7e4b0-196">推奨される HttpPost Edit のコード:読み取りと更新</span><span class="sxs-lookup"><span data-stu-id="7e4b0-196">Recommended HttpPost Edit code: Read and update</span></span>

<span data-ttu-id="7e4b0-197">HttpPost の Edit アクション メソッドを、次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-197">Replace the HttpPost Edit action method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

<span data-ttu-id="7e4b0-198">これらの変更は、過剰ポスティングを防ぐためのセキュリティのベスト プラクティスを実装します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-198">These changes implement a security best practice to prevent overposting.</span></span> <span data-ttu-id="7e4b0-199">スキャフォルダーは、`Bind` 属性を生成し、モデル バインダーによって作成されたエンティティを、`Modified` フラグが設定されたエンティティ セットに追加していました。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-199">The scaffolder generated a `Bind` attribute and added the entity created by the model binder to the entity set with a `Modified` flag.</span></span> <span data-ttu-id="7e4b0-200">そのコードは、`Include` パラメーターにリストされていないフィールド内の既存のデータを `Bind` 属性がクリアするため、多くのシナリオでは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-200">That code isn't recommended for many scenarios because the `Bind` attribute clears out any pre-existing data in fields not listed in the `Include` parameter.</span></span>

<span data-ttu-id="7e4b0-201">新しいコードは、既存のエンティティを読み取り、`TryUpdateModel` を呼び出して、取得されたエンティティのフィールドを、[ポストされたフォーム データでのユーザー入力に基づいて](xref:mvc/models/model-binding)更新します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-201">The new code reads the existing entity and calls `TryUpdateModel` to update fields in the retrieved entity [based on user input in the posted form data](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7e4b0-202">Entity Framework の自動変更追跡は、フォーム入力によって変更されるフィールドの `Modified` フラグを設定します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-202">The Entity Framework's automatic change tracking sets the `Modified` flag on the fields that are changed by form input.</span></span> <span data-ttu-id="7e4b0-203">`SaveChanges` メソッドが呼び出されると、Entity Framework はデータベースの行を更新する SQL ステートメントを作成します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-203">When the `SaveChanges` method is called, the Entity Framework creates SQL statements to update the database row.</span></span> <span data-ttu-id="7e4b0-204">コンカレンシーの競合は無視され、ユーザーによって更新されたテーブルの列のみが、データベースで更新されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-204">Concurrency conflicts are ignored, and only the table columns that were updated by the user are updated in the database.</span></span> <span data-ttu-id="7e4b0-205">(コンカレンシーの競合の処理方法は後のチュートリアルで示します。)</span><span class="sxs-lookup"><span data-stu-id="7e4b0-205">(A later tutorial shows how to handle concurrency conflicts.)</span></span>

<span data-ttu-id="7e4b0-206">過剰ポスティングを防ぐ方法のベスト プラクティスとしては、 **[Edit]** ページによって更新可能にするフィールドを、`TryUpdateModel` パラメーターでホワイトリストに登録します</span><span class="sxs-lookup"><span data-stu-id="7e4b0-206">As a best practice to prevent overposting, the fields that you want to be updateable by the **Edit** page are whitelisted in the `TryUpdateModel` parameters.</span></span> <span data-ttu-id="7e4b0-207">(パラメーター リストでフィールドのリストの前にある空の文字列は、フォーム フィールド名で使うプレフィックス用です)。現在、他に保護しているフィールドはありませんが、モデル バインダーでバインドしたいフィールドをリストに入れておくと、後でデータ モデルにフィールドを追加した場合に、ここでフィールドを明示的に追加するまで、自動的にフィールドを保護できます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-207">(The empty string preceding the list of fields in the parameter list is for a prefix to use with the form fields names.) Currently there are no extra fields that you're protecting, but listing the fields that you want the model binder to bind ensures that if you add fields to the data model in the future, they're automatically protected until you explicitly add them here.</span></span>

<span data-ttu-id="7e4b0-208">これらの変更の結果として、HttpPost の `Edit` メソッドのシグネチャは、HttpGet の `Edit` メソッドと同じになります。したがって、`EditPost` メソッドの名前を変更してあります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-208">As a result of these changes, the method signature of the HttpPost `Edit` method is the same as the HttpGet `Edit` method; therefore you've renamed the method `EditPost`.</span></span>

### <a name="alternative-httppost-edit-code-create-and-attach"></a><span data-ttu-id="7e4b0-209">代わりの HttpPost Edit コード:作成とアタッチ</span><span class="sxs-lookup"><span data-stu-id="7e4b0-209">Alternative HttpPost Edit code: Create and attach</span></span>

<span data-ttu-id="7e4b0-210">HttpPost の Edit の推奨されるコードでは、変更された列のみが更新され、モデル バインドに含めたくないプロパティのデータは維持されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-210">The recommended HttpPost edit code ensures that only changed columns get updated and preserves data in properties that you don't want included for model binding.</span></span> <span data-ttu-id="7e4b0-211">ただし、読み取り優先アプローチではデータベースの余分な読み取りが必要であり、コンカレンシーの競合を処理するためのコードが複雑になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-211">However, the read-first approach requires an extra database read, and can result in more complex code for handling concurrency conflicts.</span></span> <span data-ttu-id="7e4b0-212">代わりの方法としては、モデル バインダーによって作成されたエンティティを EF コンテキストにアタッチし、変更済みとしてマークします</span><span class="sxs-lookup"><span data-stu-id="7e4b0-212">An alternative is to attach an entity created by the model binder to the EF context and mark it as modified.</span></span> <span data-ttu-id="7e4b0-213">(次のコードはオプションのアプローチを示すためだけに掲載してあるので、このコードでプロジェクトを更新しないでください)。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-213">(Don't update your project with this code, it's only shown to illustrate an optional approach.)</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

<span data-ttu-id="7e4b0-214">この方法は、Web ページの UI にエンティティのすべてのフィールドが含まれ、そのどれでも更新できる場合に使うことができます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-214">You can use this approach when the web page UI includes all of the fields in the entity and can update any of them.</span></span>

<span data-ttu-id="7e4b0-215">スキャフォールディングされたコードは、作成とアタッチの方法を使いますが、`DbUpdateConcurrencyException` 例外のみをキャッチし、404 エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-215">The scaffolded code uses the create-and-attach approach but only catches `DbUpdateConcurrencyException` exceptions and returns 404 error codes.</span></span>  <span data-ttu-id="7e4b0-216">例では、すべてのデータベース更新例外をキャッチし、エラー メッセージを表示する方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-216">The example shown catches any database update exception and displays an error message.</span></span>

### <a name="entity-states"></a><span data-ttu-id="7e4b0-217">エンティティの状態</span><span class="sxs-lookup"><span data-stu-id="7e4b0-217">Entity States</span></span>

<span data-ttu-id="7e4b0-218">データベース コンテキストは、メモリ内のエンティティがデータベースの対応する行と同期しているかどうかを追跡しており、この情報により、`SaveChanges` メソッドを呼び出したときの処理が決まります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-218">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database, and this information determines what happens when you call the `SaveChanges` method.</span></span> <span data-ttu-id="7e4b0-219">たとえば、新しいエンティティを `Add` メソッドに渡すと、そのエンティティの状態は `Added` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-219">For example, when you pass a new entity to the `Add` method, that entity's state is set to `Added`.</span></span> <span data-ttu-id="7e4b0-220">その後、`SaveChanges` メソッドを呼び出すと、データベース コンテキストは SQL の INSERT コマンドを発行します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-220">Then when you call the `SaveChanges` method, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="7e4b0-221">エンティティは、次のいずれかの状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-221">An entity may be in one of the following states:</span></span>

* <span data-ttu-id="7e4b0-222">`Added`。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-222">`Added`.</span></span> <span data-ttu-id="7e4b0-223">エンティティはデータベースにまだ存在しません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-223">The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="7e4b0-224">`SaveChanges` メソッドは INSERT ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-224">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="7e4b0-225">`Unchanged`。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-225">`Unchanged`.</span></span> <span data-ttu-id="7e4b0-226">`SaveChanges` メソッドはこのエンティティに対し何も行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-226">Nothing needs to be done with this entity by the `SaveChanges` method.</span></span> <span data-ttu-id="7e4b0-227">データベースからエンティティを読み取ると、エンティティはこの状態で開始します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-227">When you read an entity from the database, the entity starts out with this status.</span></span>

* <span data-ttu-id="7e4b0-228">`Modified`。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-228">`Modified`.</span></span> <span data-ttu-id="7e4b0-229">エンティティのプロパティ値の一部またはすべてが変更されています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-229">Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="7e4b0-230">`SaveChanges` メソッドは UPDATE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-230">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="7e4b0-231">`Deleted`。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-231">`Deleted`.</span></span> <span data-ttu-id="7e4b0-232">エンティティには削除のマークが付けられています。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-232">The entity has been marked for deletion.</span></span> <span data-ttu-id="7e4b0-233">`SaveChanges` メソッドは DELETE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-233">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="7e4b0-234">`Detached`。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-234">`Detached`.</span></span> <span data-ttu-id="7e4b0-235">エンティティはデータベース コンテキストによって追跡されていません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-235">The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="7e4b0-236">デスクトップ アプリケーションにおいて、通常、状態の変更は自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-236">In a desktop application, state changes are typically set automatically.</span></span> <span data-ttu-id="7e4b0-237">エンティティを読み取って一部のプロパティの値を変更すると、</span><span class="sxs-lookup"><span data-stu-id="7e4b0-237">You read an entity and make changes to some of its property values.</span></span> <span data-ttu-id="7e4b0-238">そのエンティティの状態は自動的に `Modified` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-238">This causes its entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="7e4b0-239">その後、`SaveChanges` を呼び出すと、Entity Framework は、変更された実際のプロパティのみを更新する SQL UPDATE ステートメントを生成します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-239">Then when you call `SaveChanges`, the Entity Framework generates a SQL UPDATE statement that updates only the actual properties that you changed.</span></span>

<span data-ttu-id="7e4b0-240">Web アプリでは、最初にエンティティを読み取って編集されるデータを表示する `DbContext` は、ページがレンダリングされた後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-240">In a web app, the `DbContext` that initially reads an entity and displays its data to be edited is disposed after a page is rendered.</span></span> <span data-ttu-id="7e4b0-241">HttpPost の `Edit` アクション メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-241">When the HttpPost `Edit` action method is called,  a new web request is made and you have a new instance of the `DbContext`.</span></span> <span data-ttu-id="7e4b0-242">その新しいコンテキストでエンティティを再び読み取った場合、デスクトップの処理をシミュレートすることになります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-242">If you re-read the entity in that new context, you simulate desktop processing.</span></span>

<span data-ttu-id="7e4b0-243">ただし、余分な読み取り操作を行いたくない場合は、モデル バインダーによって作成されるエンティティ オブジェクトを使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-243">But if you don't want to do the extra read operation, you have to use the entity object created by the model binder.</span></span>  <span data-ttu-id="7e4b0-244">これを行う最も簡単な方法は、前に示した代替 HttpPost Edit コードと同じように、エンティティの状態を Modified に設定します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-244">The simplest way to do this is to set the entity state to Modified as is done in the alternative HttpPost Edit code shown earlier.</span></span> <span data-ttu-id="7e4b0-245">その後、`SaveChanges` を呼び出すと、コンテキストには変更されたプロパティを特定する方法がないため、Entity Framework はデータベース行のすべての列を更新します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-245">Then when you call `SaveChanges`, the Entity Framework updates all columns of the database row, because the context has no way to know which properties you changed.</span></span>

<span data-ttu-id="7e4b0-246">読み取り優先アプローチを使わずに、ユーザーが実際に変更したフィールドのみを SQL UPDATE ステートメントで更新したい場合は、コードがさらに複雑になります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-246">If you want to avoid the read-first approach, but you also want the SQL UPDATE statement to update only the fields that the user actually changed, the code is more complex.</span></span> <span data-ttu-id="7e4b0-247">HttpPost `Edit` メソッドが呼び出されたときに元の値を使用できるよう、何らかの方法 (非表示フィールドなど) を使って元の値を保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-247">You have to save the original values in some way (such as by using hidden fields) so that they're available when the HttpPost `Edit` method is called.</span></span> <span data-ttu-id="7e4b0-248">その後、元の値を使って Student エンティティを作成し、エンティティの元のバージョンで `Attach` メソッドを呼び出して、エンティティの値を新しい値に更新した後、`SaveChanges` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-248">Then you can create a Student entity using the original values, call the `Attach` method with that original version of the entity, update the entity's values to the new values, and then call `SaveChanges`.</span></span>

### <a name="test-the-edit-page"></a><span data-ttu-id="7e4b0-249">Edit ページをテストする</span><span class="sxs-lookup"><span data-stu-id="7e4b0-249">Test the Edit page</span></span>

<span data-ttu-id="7e4b0-250">アプリを実行し、 **[Students]** タブを選んで、 **[Edit]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-250">Run the app, select the **Students** tab, then click an **Edit** hyperlink.</span></span>

![Students/Edit ページ](crud/_static/student-edit.png)

<span data-ttu-id="7e4b0-252">データをいくつか変更し、 **[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-252">Change some of the data and click **Save**.</span></span> <span data-ttu-id="7e4b0-253">**[Index]** ページが開き、変更したデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-253">The **Index** page opens and you see the changed data.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="7e4b0-254">削除ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-254">Update the Delete page</span></span>

<span data-ttu-id="7e4b0-255">*StudentController.cs* に含まれる HttpGet の `Delete` メソッドのテンプレート コードは、`SingleOrDefaultAsync` メソッドを使って、選ばれた Student エンティティを取得します (Details および Edit メソッドと同様です)。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-255">In *StudentController.cs*, the template code for the HttpGet `Delete` method uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the Details and Edit methods.</span></span> <span data-ttu-id="7e4b0-256">ただし、`SaveChanges` の呼び出しが失敗したときのカスタム エラー メッセージを実装するには、何らかの機能とその対応するビューをこのメソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-256">However, to implement a custom error message when the call to `SaveChanges` fails, you'll add some functionality to this method and its corresponding view.</span></span>

<span data-ttu-id="7e4b0-257">更新および作成操作で見たように、削除操作にも 2 つのアクション メソッドが必要です。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-257">As you saw for update and create operations, delete operations require two action methods.</span></span> <span data-ttu-id="7e4b0-258">GET 要求に応答して呼び出されるメソッドは、ユーザーが削除操作を承認またはキャンセルできるビューを表示します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-258">The method that's called in response to a GET request displays a view that gives the user a chance to approve or cancel the delete operation.</span></span> <span data-ttu-id="7e4b0-259">ユーザーが操作を承認すると、POST 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-259">If the user approves it, a POST request is created.</span></span> <span data-ttu-id="7e4b0-260">その場合、HttpPost `Delete` メソッドが呼び出され、そのメソッドが実際の削除操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-260">When that happens, the HttpPost `Delete` method is called and then that method actually performs the delete operation.</span></span>

<span data-ttu-id="7e4b0-261">データベース更新時に発生する可能性のあるエラーを処理するには、try-catch ブロックを HttpPost の `Delete` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-261">You'll add a try-catch block to the HttpPost `Delete` method to handle any errors that might occur when the database is updated.</span></span> <span data-ttu-id="7e4b0-262">エラーが発生した場合、HttpPost Delete メソッドは HttpGet Delete メソッドを呼び出して、エラーが発生したことを示すパラメーターを渡します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-262">If an error occurs, the HttpPost Delete method calls the HttpGet Delete method, passing it a parameter that indicates that an error has occurred.</span></span> <span data-ttu-id="7e4b0-263">HttpGet Delete メソッドは、確認ページとエラー メッセージを再び表示し、ユーザーがもう一度実行するか取り消すことができるようにします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-263">The HttpGet Delete method then redisplays the confirmation page along with the error message, giving the user an opportunity to cancel or try again.</span></span>

<span data-ttu-id="7e4b0-264">HttpGet の `Delete` アクション メソッドを、エラー報告を管理する次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-264">Replace the HttpGet `Delete` action method with the following code, which manages error reporting.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

<span data-ttu-id="7e4b0-265">このコードは、変更保存の失敗後にメソッドが呼び出されたかどうかを示す省略可能なパラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-265">This code accepts an optional parameter that indicates whether the method was called after a failure to save changes.</span></span> <span data-ttu-id="7e4b0-266">HttpGet `Delete` メソッドが呼び出される前にエラーが発生していない場合、このパラメーターは false に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-266">This parameter is false when the HttpGet `Delete` method is called without a previous failure.</span></span> <span data-ttu-id="7e4b0-267">データベース更新エラーに対して HttpPost の `Delete` メソッドがこのメソッドを呼び出した場合、パラメーターは true で、エラー メッセージがビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-267">When it's called by the HttpPost `Delete` method in response to a database update error, the parameter is true and an error message is passed to the view.</span></span>

### <a name="the-read-first-approach-to-httppost-delete"></a><span data-ttu-id="7e4b0-268">HttpPost Delete の読み取り優先アプローチ</span><span class="sxs-lookup"><span data-stu-id="7e4b0-268">The read-first approach to HttpPost Delete</span></span>

<span data-ttu-id="7e4b0-269">HttpPost の `Delete` アクション メソッド (名前は `DeleteConfirmed`) を、次のコードに置き換えます。このコードは、実際の削除操作を実行して、データベース更新エラーをキャッチします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-269">Replace the HttpPost `Delete` action method (named `DeleteConfirmed`) with the following code, which performs the actual delete operation and catches any database update errors.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

<span data-ttu-id="7e4b0-270">このコードは、選択されたエンティティを取得した後、`Remove` メソッドを呼び出して、エンティティの状態を `Deleted` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-270">This code retrieves the selected entity, then calls the `Remove` method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="7e4b0-271">`SaveChanges` が呼び出されると、SQL DELETE コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-271">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span>

### <a name="the-create-and-attach-approach-to-httppost-delete"></a><span data-ttu-id="7e4b0-272">HttpPost Delete の作成とアタッチ アプローチ</span><span class="sxs-lookup"><span data-stu-id="7e4b0-272">The create-and-attach approach to HttpPost Delete</span></span>

<span data-ttu-id="7e4b0-273">大規模なアプリケーションでのパフォーマンス向上が優先される場合は、主キーの値のみを使って Student エンティティをインスタンス化し、エンティティの状態を `Deleted` に設定することによって、不必要な SQL クエリが実行されないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-273">If improving performance in a high-volume application is a priority, you could avoid an unnecessary SQL query by instantiating a Student entity using only the primary key value and then setting the entity state to `Deleted`.</span></span> <span data-ttu-id="7e4b0-274">エンティティを削除するために Entity Framework に必要なものは主キーの値だけです</span><span class="sxs-lookup"><span data-stu-id="7e4b0-274">That's all that the Entity Framework needs in order to delete the entity.</span></span> <span data-ttu-id="7e4b0-275">(このコードは、代替手段の説明のためにのみ示してあるので、プロジェクトに追加しないでください)。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-275">(Don't put this code in your project; it's here just to illustrate an alternative.)</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

<span data-ttu-id="7e4b0-276">エンティティに関連するデータも削除する必要がある場合は、連鎖削除をデータベースで構成します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-276">If the entity has related data that should also be deleted, make sure that cascade delete is configured in the database.</span></span> <span data-ttu-id="7e4b0-277">このエンティティ削除方法では、削除する関連エンティティがあることを EF が認識しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-277">With this approach to entity deletion, EF might not realize there are related entities to be deleted.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="7e4b0-278">Delete ビューを更新する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-278">Update the Delete view</span></span>

<span data-ttu-id="7e4b0-279">*Views/Student/Delete.cshtml* で、次の例に示すように、h2 見出しと h3 見出しの間にエラー メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-279">In *Views/Student/Delete.cshtml*, add an error message between the h2 heading and the h3 heading, as shown in the following example:</span></span>

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

<span data-ttu-id="7e4b0-280">アプリを実行し、 **[Students]** タブを選んで、 **[Delete]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-280">Run the app, select the **Students** tab, and click a **Delete** hyperlink:</span></span>

![削除確認ページ](crud/_static/student-delete.png)

<span data-ttu-id="7e4b0-282">**[Delete]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-282">Click **Delete**.</span></span> <span data-ttu-id="7e4b0-283">削除された学生を含まない [Index] ページが表示されます</span><span class="sxs-lookup"><span data-stu-id="7e4b0-283">The Index page is displayed without the deleted student.</span></span> <span data-ttu-id="7e4b0-284">(アクションでのエラー処理コードの例は、コンカレンシーチュートリアルをご覧ください。)</span><span class="sxs-lookup"><span data-stu-id="7e4b0-284">(You'll see an example of the error handling code in action in the concurrency tutorial.)</span></span>

## <a name="close-database-connections"></a><span data-ttu-id="7e4b0-285">データベース接続を閉じる</span><span class="sxs-lookup"><span data-stu-id="7e4b0-285">Close database connections</span></span>

<span data-ttu-id="7e4b0-286">データベース接続が保持しているリソースを解放するには、使い終わったコンテキスト インスタンスをできるだけ早く破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-286">To free up the resources that a database connection holds, the context instance must be disposed as soon as possible when you are done with it.</span></span> <span data-ttu-id="7e4b0-287">ASP.NET Core に組み込まれている[依存関係の挿入](../../fundamentals/dependency-injection.md)が、そのタスクを自動的に行います。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-287">The ASP.NET Core built-in [dependency injection](../../fundamentals/dependency-injection.md) takes care of that task for you.</span></span>

<span data-ttu-id="7e4b0-288">*Startup.cs* で、[AddDbContext 拡張メソッド](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs)を呼び出して、ASP.NET Core DI コンテナー内の `DbContext` クラスをプロビジョニングします。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-288">In *Startup.cs*, you call the [AddDbContext extension method](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) to provision the `DbContext` class in the ASP.NET Core DI container.</span></span> <span data-ttu-id="7e4b0-289">このメソッドは、サービスの有効期間を既定で `Scoped` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-289">That method sets the service lifetime to `Scoped` by default.</span></span> <span data-ttu-id="7e4b0-290">`Scoped` はコンテキスト オブジェクトの有効期間が Web 要求の有効期間と一致することを意味し、Web 要求の最後に `Dispose` メソッドが自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-290">`Scoped` means the context object lifetime coincides with the web request life time, and the `Dispose` method will be called automatically at the end of the web request.</span></span>

## <a name="handle-transactions"></a><span data-ttu-id="7e4b0-291">トランザクションを処理する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-291">Handle transactions</span></span>

<span data-ttu-id="7e4b0-292">既定では、Entity Framework はトランザクションを暗黙的に実装します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-292">By default the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="7e4b0-293">複数の行またはテーブルを変更してから `SaveChanges` を呼び出すシナリオでは、Entity Framework によって自動的に、すべての変更が成功するか、またはすべての変更が失敗することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-293">In scenarios where you make changes to multiple rows or tables and then call `SaveChanges`, the Entity Framework automatically makes sure that either all of your changes succeed or they all fail.</span></span> <span data-ttu-id="7e4b0-294">一部の変更が完了した後でエラーが発生した場合、それらの変更は自動的にロールバックされます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-294">If some changes are done first and then an error happens, those changes are automatically rolled back.</span></span> <span data-ttu-id="7e4b0-295">たとえば、Entity Framework の外部で行われる操作をトランザクションに含めたい場合など、より詳細な制御が必要なシナリオについては、「[Using Transactions](/ef/core/saving/transactions)」(トランザクションの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-295">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="no-tracking-queries"></a><span data-ttu-id="7e4b0-296">追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="7e4b0-296">No-tracking queries</span></span>

<span data-ttu-id="7e4b0-297">データベース コンテキストは、テーブルの行を取得してそれらを表すエンティティ オブジェクトを作成するとき、既定では、メモリ内のエンティティがデータベース内のデータと同期されているかどうかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-297">When a database context retrieves table rows and creates entity objects that represent them, by default it keeps track of whether the entities in memory are in sync with what's in the database.</span></span> <span data-ttu-id="7e4b0-298">メモリ内のデータはキャッシュとして機能し、エンティティを更新するときに使われます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-298">The data in memory acts as a cache and is used when you update an entity.</span></span> <span data-ttu-id="7e4b0-299">Web アプリケーションでは、一般にコンテキスト インスタンスの存続期間は短く (要求ごとに新しいインスタンスが作成されて破棄されます)、通常、エンティティを読み取るコンテキストはエンティティが再び使われる前に破棄されるので、多くの場合、このようなキャッシュは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-299">This caching is often unnecessary in a web application because context instances are typically short-lived (a new one is created and disposed for each request) and the context that reads an entity is typically disposed before that entity is used again.</span></span>

<span data-ttu-id="7e4b0-300">`AsNoTracking` メソッドを呼び出すことで、メモリ内のエンティティ オブジェクトの追跡を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-300">You can disable tracking of entity objects in memory by calling the `AsNoTracking` method.</span></span> <span data-ttu-id="7e4b0-301">追跡を無効にした方がよい一般的なシナリオを以下に示します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-301">Typical scenarios in which you might want to do that include the following:</span></span>

* <span data-ttu-id="7e4b0-302">コンテキストの有効期間中に、どのエンティティも更新する必要がなく、EF が[個別のクエリによって取得されるエンティティと共にナビゲーション プロパティを自動的に読み込む](read-related-data.md)必要がない場合。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-302">During the context lifetime you don't need to update any entities, and you don't need EF to [automatically load navigation properties with  entities retrieved by separate queries](read-related-data.md).</span></span> <span data-ttu-id="7e4b0-303">コントローラーの HttpGet アクション メソッドでは、このような状況が頻繁に発生します。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-303">Frequently these conditions are met in a controller's HttpGet action methods.</span></span>

* <span data-ttu-id="7e4b0-304">大量のデータを取得するクエリを実行していて、返されるデータの小さな部分だけが更新される場合。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-304">You are running a query that retrieves a large volume of data, and only a small portion of the returned data will be updated.</span></span> <span data-ttu-id="7e4b0-305">大規模なクエリでは、追跡をオフにして、更新が必要な少数のエンティティに対して後でクエリを実行する方が、効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-305">It may be more efficient to turn off tracking for the large query, and run a query later for the few entities that need to be updated.</span></span>

* <span data-ttu-id="7e4b0-306">エンティティを更新するためにエンティティをアタッチしたいが、それより前に別の目的で同じエンティティを取得してある場合。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-306">You want to attach an entity in order to update it, but earlier you retrieved the same entity for a different purpose.</span></span> <span data-ttu-id="7e4b0-307">エンティティはデータベース コンテキストによって既に追跡されているため、変更するエンティティをアタッチできません。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-307">Because the entity is already being tracked by the database context, you can't attach the entity that you want to change.</span></span> <span data-ttu-id="7e4b0-308">このような状況に対処する方法の 1 つは、前のクエリで `AsNoTracking` を呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-308">One way to handle this situation is to call `AsNoTracking` on the earlier query.</span></span>

<span data-ttu-id="7e4b0-309">詳細については、「[Tracking vs.No-Tracking Queries](/ef/core/querying/tracking)」(追跡ありクエリと追跡なしクエリ) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-309">For more information, see [Tracking vs. No-Tracking](/ef/core/querying/tracking).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="7e4b0-310">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="7e4b0-310">Get the code</span></span>

[<span data-ttu-id="7e4b0-311">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-311">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="7e4b0-312">次の手順</span><span class="sxs-lookup"><span data-stu-id="7e4b0-312">Next steps</span></span>

<span data-ttu-id="7e4b0-313">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-313">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7e4b0-314">Details ページをカスタマイズした</span><span class="sxs-lookup"><span data-stu-id="7e4b0-314">Customized the Details page</span></span>
> * <span data-ttu-id="7e4b0-315">Create ページを更新した</span><span class="sxs-lookup"><span data-stu-id="7e4b0-315">Updated the Create page</span></span>
> * <span data-ttu-id="7e4b0-316">Edit ページを更新した</span><span class="sxs-lookup"><span data-stu-id="7e4b0-316">Updated the Edit page</span></span>
> * <span data-ttu-id="7e4b0-317">Delete ページを更新した</span><span class="sxs-lookup"><span data-stu-id="7e4b0-317">Updated the Delete page</span></span>
> * <span data-ttu-id="7e4b0-318">データベース接続を閉じた</span><span class="sxs-lookup"><span data-stu-id="7e4b0-318">Closed database connections</span></span>

<span data-ttu-id="7e4b0-319">並べ替え、フィルター処理、ページングを追加して **Index** ページの機能を拡張する方法について学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="7e4b0-319">Advance to the next tutorial to learn how to expand the functionality of the **Index** page by adding sorting, filtering, and paging.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7e4b0-320">次へ: 並べ替え、フィルター処理、ページング</span><span class="sxs-lookup"><span data-stu-id="7e4b0-320">Next: Sorting, filtering, and paging</span></span>](sort-filter-page.md)
