---
title: 'チュートリアル: 複合データ モデルを作成する - ASP.NET MVC と EF Core'
description: このチュートリアルでは、エンティティとリレーションシップをさらに追加し、書式設定、検証、マッピングの規則を指定してデータ モデルをカスタマイズします。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: b8b1ade4c8c29d34200bf8c0944cff6adec0bb95
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288963"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="91154-103">チュートリアル: 複合データ モデルを作成する - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="91154-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="91154-104">前のチュートリアルでは、3 つのエンティティで構成された単純なデータ モデルを使用して作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="91154-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="91154-105">このチュートリアルでは、エンティティとリレーションシップをさらに追加し、書式設定、検証、データベース マッピングの規則を指定してデータ モデルをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="91154-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="91154-106">完了すると、エンティティ クラスは、以下の図のように完成したデータ モデルを構成します。</span><span class="sxs-lookup"><span data-stu-id="91154-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

<span data-ttu-id="91154-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="91154-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91154-109">データ モデルをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="91154-109">Customize the Data model</span></span>
> * <span data-ttu-id="91154-110">Student エンティティに変更を加える</span><span class="sxs-lookup"><span data-stu-id="91154-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="91154-111">Instructor エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="91154-111">Create Instructor entity</span></span>
> * <span data-ttu-id="91154-112">OfficeAssignment エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="91154-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="91154-113">Course エンティティを変更する</span><span class="sxs-lookup"><span data-stu-id="91154-113">Modify Course entity</span></span>
> * <span data-ttu-id="91154-114">Department エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="91154-114">Create Department entity</span></span>
> * <span data-ttu-id="91154-115">Enrollment エンティティを変更する</span><span class="sxs-lookup"><span data-stu-id="91154-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="91154-116">データベース コンテキストを更新する</span><span class="sxs-lookup"><span data-stu-id="91154-116">Update the database context</span></span>
> * <span data-ttu-id="91154-117">テスト データを使ってデータベースをシードする</span><span class="sxs-lookup"><span data-stu-id="91154-117">Seed database with test data</span></span>
> * <span data-ttu-id="91154-118">移行を追加する</span><span class="sxs-lookup"><span data-stu-id="91154-118">Add a migration</span></span>
> * <span data-ttu-id="91154-119">接続文字列を変更する</span><span class="sxs-lookup"><span data-stu-id="91154-119">Change the connection string</span></span>
> * <span data-ttu-id="91154-120">データベースを更新する</span><span class="sxs-lookup"><span data-stu-id="91154-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="91154-121">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="91154-121">Prerequisites</span></span>

* [<span data-ttu-id="91154-122">EF Core 移行を使用する</span><span class="sxs-lookup"><span data-stu-id="91154-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="91154-123">データ モデルをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="91154-123">Customize the Data model</span></span>

<span data-ttu-id="91154-124">このセクションでは、書式設定、検証、データベース マッピング規則を指定する属性を使用して、データ モデルをカスタマイズする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="91154-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="91154-125">その後、次のいくつかのセクションで、作成済みのクラスに属性を追加し、モデルの残りのエンティティ型に対して新しいクラスを作成して、完全な School データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="91154-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="91154-126">DataType 属性</span><span class="sxs-lookup"><span data-stu-id="91154-126">The DataType attribute</span></span>

<span data-ttu-id="91154-127">学生の登録日について、すべての Web ページでは現在、日付と共に時刻が表示されていますが、このフィールドでは日付が重要になります。</span><span class="sxs-lookup"><span data-stu-id="91154-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="91154-128">データ注釈属性を使用すれば、1 つのコードを変更するだけで、データを表示するすべてのビューの表示形式を修正できます。</span><span class="sxs-lookup"><span data-stu-id="91154-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="91154-129">その方法例を表示するには、`Student` クラスの `EnrollmentDate` プロパティに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="91154-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="91154-130">*Models/Student.cs* で、次の例のように、`System.ComponentModel.DataAnnotations` 名前空間の `using` ステートメントを追加し、`DataType` および `DisplayFormat` 属性を `EnrollmentDate` プロパティに追加します。</span><span class="sxs-lookup"><span data-stu-id="91154-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="91154-131">`DataType` 属性は、データベースの組み込み型よりも具体的なデータ型を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="91154-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="91154-132">この例では、追跡する必要があるのは、日付と時刻ではなく、日付のみです。</span><span class="sxs-lookup"><span data-stu-id="91154-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="91154-133">`DataType` 列挙型は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くの型のために用意されています。</span><span class="sxs-lookup"><span data-stu-id="91154-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="91154-134">また、`DataType` 属性を使用して、アプリケーションで型固有の機能を自動的に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="91154-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="91154-135">たとえば、`mailto:` リンクを `DataType.EmailAddress` に作成したり、HTML5 をサポートするブラウザーで `DataType.Date` に日付セレクターを提供したりできます。</span><span class="sxs-lookup"><span data-stu-id="91154-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="91154-136">`DataType` 属性は、HTML 5 ブラウザーが認識できる HTML 5 `data-` ("データ ダッシュ" と読みます) 属性を出力します。</span><span class="sxs-lookup"><span data-stu-id="91154-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="91154-137">`DataType` 属性では検証が提供されません。</span><span class="sxs-lookup"><span data-stu-id="91154-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="91154-138">`DataType.Date` は、表示される日付の書式を指定しません。</span><span class="sxs-lookup"><span data-stu-id="91154-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="91154-139">既定では、データ フィールドはサーバーの CultureInfo に基づき、既定の書式に従って表示されます。</span><span class="sxs-lookup"><span data-stu-id="91154-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="91154-140">`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="91154-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="91154-141">`ApplyFormatInEditMode` の設定では、編集用にテキスト ボックスに値を表示するときにも適用する必要がある書式設定を指定します</span><span class="sxs-lookup"><span data-stu-id="91154-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="91154-142">(フィールドによっては適用したくないこともあります。たとえば、通貨値では、編集用テキスト ボックスには通貨記号が必要でない場合があります)。</span><span class="sxs-lookup"><span data-stu-id="91154-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="91154-143">`DisplayFormat` 属性を単独で使用できますが、一般的に、`DataType` 属性も使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="91154-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="91154-144">`DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。また、`DisplayFormat` にはない、次の利点があります。</span><span class="sxs-lookup"><span data-stu-id="91154-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="91154-145">ブラウザーは HTML5 機能を有効にすることができます (たとえば、カレンダー コントロール、ロケールに適した通貨記号、メール リンク、一部のクライアント側の入力検証を表示するときなど)。</span><span class="sxs-lookup"><span data-stu-id="91154-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="91154-146">ブラウザーの既定では、ロケールに基づいて正しい書式を使ってデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="91154-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="91154-147">詳細については、[\<入力> タグ ヘルパーに関するドキュメント](../../mvc/views/working-with-forms.md#the-input-tag-helper)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91154-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="91154-148">アプリを実行し、Students インデックス ページに移動すると、登録日の時刻が表示されなくなっていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="91154-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="91154-149">Student モデルを使用するどのビューでも同様です。</span><span class="sxs-lookup"><span data-stu-id="91154-149">The same will be true for any view that uses the Student model.</span></span>

![時刻なしの日付が表示されている Students インデックス ページ](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="91154-151">StringLength 属性</span><span class="sxs-lookup"><span data-stu-id="91154-151">The StringLength attribute</span></span>

<span data-ttu-id="91154-152">属性を使用して、データ検証規則と検証エラー メッセージを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="91154-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="91154-153">`StringLength` 属性では、データベース内の最大長が設定され、ASP.NET Core MVC に対するクライアント側とサーバー側の検証が提供されます。</span><span class="sxs-lookup"><span data-stu-id="91154-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="91154-154">この属性で最小長を指定することもできますが、最小値はデータベース スキーマに影響しません。</span><span class="sxs-lookup"><span data-stu-id="91154-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="91154-155">たとえば、ユーザーが 50 文字を超える名前を入力しないようにする必要があるとします。</span><span class="sxs-lookup"><span data-stu-id="91154-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="91154-156">この制限を追加するには、次の例のように、`StringLength` 属性を `LastName` および `FirstMidName` プロパティに追加します。</span><span class="sxs-lookup"><span data-stu-id="91154-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="91154-157">`StringLength` 属性では、ユーザーが名前に空白を入力しないようにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="91154-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="91154-158">`RegularExpression` 属性を使用して、入力に制限を適用することができます。</span><span class="sxs-lookup"><span data-stu-id="91154-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="91154-159">たとえば、次のコードでは、最初の文字を大文字にし、残りの文字をアルファベット順にすることを要求します。</span><span class="sxs-lookup"><span data-stu-id="91154-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="91154-160">`MaxLength` 属性は、`StringLength` 属性と同様の機能を提供しますが、クライアント側の検証は提供しません。</span><span class="sxs-lookup"><span data-stu-id="91154-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="91154-161">データベース モデルは、現在、データベース スキーマでの変更が必要な方法で変更されています。</span><span class="sxs-lookup"><span data-stu-id="91154-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="91154-162">移行を使用すれば、アプリケーション UI を使用してデータベースに追加した可能性のあるデータを失うことなく、スキーマを更新できます。</span><span class="sxs-lookup"><span data-stu-id="91154-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="91154-163">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="91154-163">Save your changes and build the project.</span></span> <span data-ttu-id="91154-164">次に、プロジェクト フォルダーでコマンド ウィンドウを開き、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="91154-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="91154-165">`migrations add` コマンドはデータ損失が発生する可能性があることを警告します。これは、変更により、2 つの列の最大長が短くなるためです。</span><span class="sxs-lookup"><span data-stu-id="91154-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="91154-166">移行時に *\<timeStamp>_MaxLengthOnNames.cs* という名前のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="91154-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="91154-167">このファイルには、現在のデータ モデルと一致するようにデータベースを更新する `Up` メソッドのコードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="91154-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="91154-168">`database update` コマンドでそのコードが実行されました。</span><span class="sxs-lookup"><span data-stu-id="91154-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="91154-169">移行ファイル名の前に付けられる timestamp は、移行を並べ替えるために Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="91154-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="91154-170">update-database コマンドを実行する前に複数の移行を作成できます。その後、すべての移行は作成順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="91154-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="91154-171">アプリを実行し、 **[Students]** タブを選択して、 **[新規作成]** をクリックし、50 文字を超えるいずれかの名前を入力してみます。</span><span class="sxs-lookup"><span data-stu-id="91154-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="91154-172">アプリケーションにより、この操作が防止されます。</span><span class="sxs-lookup"><span data-stu-id="91154-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="91154-173">Column 属性</span><span class="sxs-lookup"><span data-stu-id="91154-173">The Column attribute</span></span>

<span data-ttu-id="91154-174">属性を使用して、データベースへのクラスとプロパティのマッピング方法を制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="91154-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="91154-175">フィールドにミドル ネームも含まれている場合があるため、名フィールドに対して `FirstMidName` という名前を使用したとします。</span><span class="sxs-lookup"><span data-stu-id="91154-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="91154-176">ただし、データベース列は `FirstName` という名前にする必要があります。これは、データベースに対するアドホック クエリを記述するユーザーがその名前に慣れているためです。</span><span class="sxs-lookup"><span data-stu-id="91154-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="91154-177">このマッピングを作成する場合、`Column` 属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="91154-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="91154-178">`Column` 属性は、データベースの作成時に、`FirstMidName` プロパティにマップする `Student` テーブルの列が `FirstName` という名前になるように指定します。</span><span class="sxs-lookup"><span data-stu-id="91154-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="91154-179">つまり、コードが `Student.FirstMidName` を参照したときに、データが `Student` テーブルの `FirstName` 列から取り込まれるか、更新されます。</span><span class="sxs-lookup"><span data-stu-id="91154-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="91154-180">列名を指定しない場合は、プロパティ名と同じ名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="91154-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="91154-181">*Student.cs* ファイルで、以下の強調表示されているコードに示されているように、`System.ComponentModel.DataAnnotations.Schema` の `using` ステートメントを追加し、列名属性を `FirstMidName` プロパティに追加します。</span><span class="sxs-lookup"><span data-stu-id="91154-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="91154-182">`Column` 属性を追加すると、`SchoolContext` をサポートするモデルが変更されるため、データベースと一致しなくなります。</span><span class="sxs-lookup"><span data-stu-id="91154-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="91154-183">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="91154-183">Save your changes and build the project.</span></span> <span data-ttu-id="91154-184">次に、プロジェクト フォルダーでコマンド ウィンドウを開き、次のコマンドを入力して別の移行を作成します。</span><span class="sxs-lookup"><span data-stu-id="91154-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="91154-185">**SQL Server オブジェクト エクスプローラー**で、**Student** テーブルをダブルクリックして、Student テーブル デザイナーを開きます。</span><span class="sxs-lookup"><span data-stu-id="91154-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![移行後の SSOX の Students テーブル](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="91154-187">最初の 2 つの移行を適用する前の名前列の型は nvarchar(MAX) でした。</span><span class="sxs-lookup"><span data-stu-id="91154-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="91154-188">現在は nvarchar(50) になっており、列名は FirstMidName から FirstName に変更されています。</span><span class="sxs-lookup"><span data-stu-id="91154-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="91154-189">次のセクションですべてのエンティティ クラスの作成を完了する前にコンパイルしようとすると、コンパイラ エラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="91154-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="91154-190">Student エンティティに対する変更</span><span class="sxs-lookup"><span data-stu-id="91154-190">Changes to Student entity</span></span>

![Student エンティティ](complex-data-model/_static/student-entity.png)

<span data-ttu-id="91154-192">*Models/Student.cs* で、前の手順で追加したコードを以下のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91154-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="91154-193">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="91154-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="91154-194">Required 属性</span><span class="sxs-lookup"><span data-stu-id="91154-194">The Required attribute</span></span>

<span data-ttu-id="91154-195">`Required` 属性では、名前プロパティの必須フィールドを作成します。</span><span class="sxs-lookup"><span data-stu-id="91154-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="91154-196">値の型 (DateTime、int、double、float など) などの null 非許容型では `Required` 属性は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="91154-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="91154-197">null にできない型は自動的に必須フィールドとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="91154-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="91154-198">`MinimumLength` を適用するには、`Required` 属性を `MinimumLength` と共に使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91154-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="91154-199">Display 属性</span><span class="sxs-lookup"><span data-stu-id="91154-199">The Display attribute</span></span>

<span data-ttu-id="91154-200">`Display` 属性は、テキスト ボックスのキャプションを、各インスタンスのプロパティ名 (単語を区切るスペースがない) の代わりに、"First Name"、"Last Name"、"Full Name"、"Enrollment Date" にするよう指定します。</span><span class="sxs-lookup"><span data-stu-id="91154-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="91154-201">FullName 集計プロパティ</span><span class="sxs-lookup"><span data-stu-id="91154-201">The FullName calculated property</span></span>

<span data-ttu-id="91154-202">`FullName` は集計プロパティであり、2 つの別のプロパティを連結して作成される値を返します。</span><span class="sxs-lookup"><span data-stu-id="91154-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="91154-203">したがって、get アクセサーのみが存在し、データベースで `FullName` 列は生成されません。</span><span class="sxs-lookup"><span data-stu-id="91154-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="91154-204">Instructor エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="91154-204">Create Instructor entity</span></span>

![Instructor エンティティ](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="91154-206">*Models/Instructor.cs* を作成し、テンプレート コードを以下のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91154-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="91154-207">Student および Instructor エンティティに同じプロパティがいくつかあることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="91154-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="91154-208">このシリーズの後半の[継承の実装](inheritance.md)に関するチュートリアルでは、冗長さをなくすため、このコードをリファクタリングします。</span><span class="sxs-lookup"><span data-stu-id="91154-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="91154-209">複数の属性 1 行に配置して、次のように `HireDate` 属性を記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="91154-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="91154-210">CourseAssignments と OfficeAssignment ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="91154-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="91154-211">`CourseAssignments` と `OfficeAssignment` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="91154-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="91154-212">講師は任意の数のコースを担当できるため、`CourseAssignments` はコレクションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="91154-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="91154-213">ナビゲーション プロパティで複数のエンティティを保持できる場合は、その型を、エンティティを追加、削除、更新できるリストにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="91154-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="91154-214">`ICollection<T>`、または `List<T>` や `HashSet<T>` などの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="91154-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="91154-215">`ICollection<T>` を指定した場合、EF では既定で `HashSet<T>` コレクションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="91154-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="91154-216">これらが `CourseAssignment` エンティティである理由は、多対多リレーションシップに関する以下のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="91154-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="91154-217">Contoso University のビジネス ルールには、講師は 1 つのオフィスのみを持つことができると示されているため、`OfficeAssignment` プロパティでは単一の OfficeAssignment エンティティが保持されます (オフィスが割り当てられていない場合は null である可能性がある)。</span><span class="sxs-lookup"><span data-stu-id="91154-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="91154-218">OfficeAssignment エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="91154-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment エンティティ](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="91154-220">以下のコードを使用して、*Models/OfficeAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="91154-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="91154-221">Key 属性</span><span class="sxs-lookup"><span data-stu-id="91154-221">The Key attribute</span></span>

<span data-ttu-id="91154-222">Instructor エンティティと OfficeAssignment エンティティの間には一対ゼロまたは一対一リレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="91154-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="91154-223">オフィスが割り当てられている講師についてのみ、オフィス割り当てが存在します。したがって、その主キーは Instructor エンティティに対する外部キーでもあります。</span><span class="sxs-lookup"><span data-stu-id="91154-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="91154-224">ただし、名前が ID や classnameID 名前付け規則に従っていないため、Entity Framework は InstructorID をこのエンティティの主キーとして自動的に認識できません。</span><span class="sxs-lookup"><span data-stu-id="91154-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="91154-225">したがって、`Key` 属性はキーとして識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="91154-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="91154-226">エンティティに独自の主キーはあっても、プロパティに classnameID や ID 以外の名前を付けたい場合は、`Key` 属性を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="91154-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="91154-227">列は依存リレーションシップに対するものであるため、既定では EF はキーを非データベース生成として扱います。</span><span class="sxs-lookup"><span data-stu-id="91154-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="91154-228">Instructor ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="91154-228">The Instructor navigation property</span></span>

<span data-ttu-id="91154-229">Instructor エンティティには null 許容の `OfficeAssignment` ナビゲーション プロパティがあり (講師にオフィスが割り当てられていない場合があるため)、OfficeAssignment エンティティには null 非許容の `Instructor` ナビゲーション プロパティがあります (講師なしではオフィス割り当てが存在できない、つまり、`InstructorID` が null 非許容であるため)。</span><span class="sxs-lookup"><span data-stu-id="91154-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="91154-230">Instructor エンティティに関連する OfficeAssignment エンティティがある場合、各エンティティにはそのナビゲーション プロパティの別のエンティティへの参照があります。</span><span class="sxs-lookup"><span data-stu-id="91154-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="91154-231">Instructor ナビゲーション プロパティに `[Required]` 属性を配置して、関連する講師が存在するように指定できますが、`InstructorID` 外部キー (このテーブルに対するキーでもある) は null 非許容であるため、そうする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="91154-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="91154-232">Course エンティティを変更する</span><span class="sxs-lookup"><span data-stu-id="91154-232">Modify Course entity</span></span>

![Course エンティティ](complex-data-model/_static/course-entity.png)

<span data-ttu-id="91154-234">*Models/Course.cs* で、前の手順で追加したコードを以下のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91154-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="91154-235">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="91154-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="91154-236">Course エンティティには外部キー プロパティ `DepartmentID` (関連する Department エンティティを指す) があり、`Department` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="91154-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="91154-237">Entity Framework では、関連エンティティのナビゲーション プロパティがある場合、ユーザーがデータ モデルに外部キー プロパティを追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="91154-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="91154-238">EF は必要に応じて、データベースで外部キーを自動的に作成し、[シャドウ プロパティ](/ef/core/modeling/shadow-properties)を作成します。</span><span class="sxs-lookup"><span data-stu-id="91154-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="91154-239">ただし、データ モデルに外部キーがある場合は、更新をより簡単かつ効率的に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="91154-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="91154-240">たとえば、編集する Course エンティティをフェッチするときに読み込まないと、Department エンティティは null になります。したがって、Course エンティティを更新する場合は、まず、Department エンティティをフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="91154-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="91154-241">外部キー プロパティ `DepartmentID` がデータ モデルに含まれている場合は、更新前に Department エンティティをフェッチする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="91154-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="91154-242">DatabaseGenerated 属性</span><span class="sxs-lookup"><span data-stu-id="91154-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="91154-243">`CourseID` プロパティで `DatabaseGenerated` 属性と `None` パラメーターを使用すると、主キー値がデータベースによって生成されるのではなく、ユーザーによって提供されるように指定されます。</span><span class="sxs-lookup"><span data-stu-id="91154-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="91154-244">既定では、Entity Framework は、主キー値がデータベースによって生成されることを前提とします。</span><span class="sxs-lookup"><span data-stu-id="91154-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="91154-245">これはほとんどのシナリオに該当します。</span><span class="sxs-lookup"><span data-stu-id="91154-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="91154-246">ただし、Course エンティティの場合、1 つの学科に 1000 シリーズ、別の学科に 2000 シリーズといったユーザー指定のコース番号を使用します。</span><span class="sxs-lookup"><span data-stu-id="91154-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="91154-247">行の作成日または更新日を記録するために使用されるデータベース列の場合のように、`DatabaseGenerated` 属性は既定値を生成するためにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="91154-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="91154-248">詳細については、「[生成される値](/ef/core/modeling/generated-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91154-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91154-249">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="91154-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="91154-250">Course エンティティの外部キー プロパティとナビゲーション プロパティには、以下のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="91154-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="91154-251">コースは 1 つの学科に割り当てられます。したがって、前述の理由により、`DepartmentID` 外部キーと `Department` ナビゲーション プロパティが存在します。</span><span class="sxs-lookup"><span data-stu-id="91154-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="91154-252">コースには任意の数の学生が登録できるため、`Enrollments` ナビゲーション プロパティはコレクションとなります。</span><span class="sxs-lookup"><span data-stu-id="91154-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="91154-253">複数の講師が 1 つのコースを担当する場合があるため、`CourseAssignments` ナビゲーション プロパティはコレクションとなります (`CourseAssignment` 型については、[後で](#many-to-many-relationships)説明します)。</span><span class="sxs-lookup"><span data-stu-id="91154-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="91154-254">Department エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="91154-254">Create Department entity</span></span>

![Department エンティティ](complex-data-model/_static/department-entity.png)

<span data-ttu-id="91154-256">以下のコードを使用して、*Models/Department.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="91154-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="91154-257">Column 属性</span><span class="sxs-lookup"><span data-stu-id="91154-257">The Column attribute</span></span>

<span data-ttu-id="91154-258">これまでは、`Column` 属性を使用して、列名のマッピングを変更しました。</span><span class="sxs-lookup"><span data-stu-id="91154-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="91154-259">Department エンティティのコードでは、`Column` 属性は SQL データ型のマッピングを変更するために使用されているため、列はデータベースの SQL Server money 型を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="91154-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="91154-260">通常、列のマッピングは必要ありません。これは、Entity Framework が、プロパティに対して定義された CLR 型に基づいて、適切な SQL Server のデータ型を選択するためです。</span><span class="sxs-lookup"><span data-stu-id="91154-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="91154-261">CLR `decimal` 型は SQL Server の `decimal` 型にマップされます。</span><span class="sxs-lookup"><span data-stu-id="91154-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="91154-262">ただし、ここでは、列に通貨額が保持されることがわかっているため、money データ型がより適しています。</span><span class="sxs-lookup"><span data-stu-id="91154-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91154-263">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="91154-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="91154-264">外部キーおよびナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="91154-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="91154-265">学科には管理者が存在する場合とそうでない場合があり、管理者は常に講師となります。</span><span class="sxs-lookup"><span data-stu-id="91154-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="91154-266">したがって、`InstructorID` プロパティは Instructor エンティティに対する外部キーとして含まれ、プロパティを null 許容とマークするために `int` 型の表記の後に疑問符が追加されます。</span><span class="sxs-lookup"><span data-stu-id="91154-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="91154-267">ナビゲーション プロパティは `Administrator` という名前ですが、Instructor エンティティを保持します。</span><span class="sxs-lookup"><span data-stu-id="91154-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="91154-268">学科には複数のコースがある場合があるため、Courses ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="91154-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="91154-269">規則により、Entity Framework では null 非許容の外部キーと多対多リレーションシップに対して連鎖削除が有効になります。</span><span class="sxs-lookup"><span data-stu-id="91154-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="91154-270">これにより、循環連鎖削除規則が適用される可能性があり、移行を追加しようとすると例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="91154-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="91154-271">たとえば、Department.InstructorID プロパティを null 許容として定義しなかった場合、EF では、講師を削除したときに学科を削除するように連鎖削除規則が構成されます。これは、発生してほしくない動作です。</span><span class="sxs-lookup"><span data-stu-id="91154-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="91154-272">ビジネス ルールで `InstructorID` プロパティを null 非許容にすることが求められた場合、以下の fluent API ステートメントを使用して、リレーションシップで連鎖削除を無効にする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="91154-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="91154-273">Enrollment エンティティを変更する</span><span class="sxs-lookup"><span data-stu-id="91154-273">Modify Enrollment entity</span></span>

![Enrollment エンティティ](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="91154-275">*Models/Enrollment.cs* で、前の手順で追加したコードを以下のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91154-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91154-276">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="91154-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="91154-277">外部キー プロパティとナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="91154-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="91154-278">登録レコードは単一のコースに対するものであるため、`CourseID` 外部キー プロパティと `Course` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="91154-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="91154-279">登録レコードは 1 人の学生に対するものであるため、`StudentID` 外部キー プロパティと `Student` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="91154-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="91154-280">多対多リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="91154-280">Many-to-Many relationships</span></span>

<span data-ttu-id="91154-281">Student エンティティと Course エンティティの間には多対多リレーションシップがあり、Enrollment エンティティはデータベースで*ペイロードがある*多対多の結合テーブルとして機能します。</span><span class="sxs-lookup"><span data-stu-id="91154-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="91154-282">"ペイロードがある" とは、Enrollment テーブルに統合テーブルの外部キー以外に追加データが含まれていることを意味します (この例では、主キーと Grade プロパティ)。</span><span class="sxs-lookup"><span data-stu-id="91154-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="91154-283">次の図は、エンティティ図でこれらのリレーションシップがどのようになるかを示しています</span><span class="sxs-lookup"><span data-stu-id="91154-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="91154-284">(この図は、EF 6.x 用の Entity Framework Power Tools を使用して生成されたものです。このチュートリアルでは図は作成しません。ここでは例として使用するだけです)。</span><span class="sxs-lookup"><span data-stu-id="91154-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Student と Course の多対多リレーションシップ](complex-data-model/_static/student-course.png)

<span data-ttu-id="91154-286">各リレーションシップ線の一方の端に 1 が、もう一方の端にアスタリスク (\*) があり、1 対多リレーションシップであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="91154-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="91154-287">Enrollment テーブルに成績情報が含まれていなかった場合、含める必要があるのは 2 つの外部キー (CourseID と StudentID) のみです。</span><span class="sxs-lookup"><span data-stu-id="91154-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="91154-288">その場合、データベースにはペイロードがない多対多結合テーブル (純粋結合テーブル) が存在することになります。</span><span class="sxs-lookup"><span data-stu-id="91154-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="91154-289">Instructor および Course エンティティにはその種の多対多リレーションシップがあり、次の手順では、ペイロードがない結合テーブルとして機能するエンティティ クラスを作成します</span><span class="sxs-lookup"><span data-stu-id="91154-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="91154-290">(EF 6.x では多対多リレーションシップの暗黙の結合テーブルがサポートされますが、EF Core ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="91154-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="91154-291">詳細については、[GitHub リポジトリの EF Core に関する記述](https://github.com/aspnet/EntityFramework/issues/1368)を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="91154-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="91154-292">CourseAssignment エンティティ</span><span class="sxs-lookup"><span data-stu-id="91154-292">The CourseAssignment entity</span></span>

![CourseAssignment エンティティ](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="91154-294">以下のコードを使用して、*Models/CourseAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="91154-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="91154-295">結合エンティティの名前</span><span class="sxs-lookup"><span data-stu-id="91154-295">Join entity names</span></span>

<span data-ttu-id="91154-296">講師対コースの多対多リレーションシップのデータベースには結合テーブルが必要であり、エンティティ セットで表す必要があります。</span><span class="sxs-lookup"><span data-stu-id="91154-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="91154-297">結合テーブルには `EntityName1EntityName2` という名前 (ここでは `CourseInstructor`) を付けるのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="91154-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="91154-298">ただし、リレーションシップを説明する名前を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="91154-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="91154-299">データ モデルは始めは単純なものであっても、後からペイロードのない結合で頻繁にペイロードが取得されるようになるため大きくなっていきます。</span><span class="sxs-lookup"><span data-stu-id="91154-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="91154-300">最初にわかりやすいエンティティ名を付けておけば、後で名前を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="91154-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="91154-301">結合エンティティでは、ビジネス ドメインに独自の自然な (場合によっては 1 単語の) 名前を指定することが理想的です。</span><span class="sxs-lookup"><span data-stu-id="91154-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="91154-302">たとえば、Books と Customers は Ratings を通じてリンクできます。</span><span class="sxs-lookup"><span data-stu-id="91154-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="91154-303">このリレーションシップの場合は、`CourseInstructor` ではなく `CourseAssignment` を選択することをお勧めます。</span><span class="sxs-lookup"><span data-stu-id="91154-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="91154-304">複合キー</span><span class="sxs-lookup"><span data-stu-id="91154-304">Composite key</span></span>

<span data-ttu-id="91154-305">外部キーが null 許容ではなく、テーブルの各行を一意に識別するために組み合わせて使用される場合、個別の主キーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="91154-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="91154-306">*InstructorID* および *CourseID* プロパティは複合主キーとして機能する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91154-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="91154-307">EF に対する複合主キーを識別する唯一の方法は、*fluent API* を使用することです (属性を使用して行うことはできません)。</span><span class="sxs-lookup"><span data-stu-id="91154-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="91154-308">次のセクションでは、複合主キーの構成方法を示します。</span><span class="sxs-lookup"><span data-stu-id="91154-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="91154-309">複合キーを使用すると、1 つのコースに対して複数の行を、また 1 人の講師に対して複数の行を使用できても、同じ講師とコースに対しては複数の行を使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="91154-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="91154-310">`Enrollment` 結合エンティティでは独自の主キーを定義するため、このような重複が考えられます。</span><span class="sxs-lookup"><span data-stu-id="91154-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="91154-311">このような重複を防ぐために、外部キー フィールドで一意のインデックスを追加するか、`CourseAssignment` と同様の複合主キーを使用して `Enrollment` を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="91154-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="91154-312">詳細については、「[インデックス](/ef/core/modeling/indexes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91154-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="91154-313">データベース コンテキストを更新する</span><span class="sxs-lookup"><span data-stu-id="91154-313">Update the database context</span></span>

<span data-ttu-id="91154-314">次の強調表示されているコードを *Data/SchoolContext.cs* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="91154-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="91154-315">このコードでは新しいエンティティが追加され、CourseAssignment エンティティの複合主キーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="91154-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="91154-316">代替手段 fluent API について</span><span class="sxs-lookup"><span data-stu-id="91154-316">About a fluent API alternative</span></span>

<span data-ttu-id="91154-317">`DbContext` クラスの `OnModelCreating` メソッドのキーでは、*fluent API* を使用して EF の動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="91154-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="91154-318">API は "fluent" と呼ばれます。これは、[EF Core のドキュメント](/ef/core/modeling/#use-fluent-api-to-configure-a-model)の例に示されているように、多くの場合、一連のメソッド呼び出しを単一のステートメントにまとめて使用されるためです。</span><span class="sxs-lookup"><span data-stu-id="91154-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="91154-319">このチュートリアルでは、属性では実行できないデータベース マッピングでのみ fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="91154-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="91154-320">ただし、fluent API を使用して、属性で実行できる書式設定、検証、およびマッピング規則のほとんどを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="91154-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="91154-321">`MinimumLength` などの一部の属性は fluent API で適用できません。</span><span class="sxs-lookup"><span data-stu-id="91154-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="91154-322">前述のとおり、`MinimumLength` はスキーマを変更せず、クライアント側とサーバー側の検証規則のみを適用します。</span><span class="sxs-lookup"><span data-stu-id="91154-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="91154-323">一部の開発者は fluent API のみを使用することを選ぶため、エンティティ クラスを "クリーン" な状態に保つことができます。</span><span class="sxs-lookup"><span data-stu-id="91154-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="91154-324">必要に応じて、属性と fluent API を組み合わせて使用することができます。fluent API のみを使用して実行できるカスタマイズがいくつかありますが、一般的は 2 つの方法のいずれかを選択して、できるだけ一貫性を保つためにそれを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="91154-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="91154-325">両方の使用時に競合が発生する場合は、Fluent API で属性がオーバーライドされることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="91154-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="91154-326">属性と fluent API の詳細については、「[構成の方法](/ef/core/modeling/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91154-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="91154-327">リレーションシップを示すエンティティ図</span><span class="sxs-lookup"><span data-stu-id="91154-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="91154-328">次の図では、完成した School モデルに対して Entity Framework Power Tools で作成される図を示します。</span><span class="sxs-lookup"><span data-stu-id="91154-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

<span data-ttu-id="91154-330">一対多リレーションシップの線 (1 対 \*) 以外にも、ここには Instructor および OfficeAssignment エンティティ間の一対ゼロまたは一対一リレーションシップの線 (1 対 0..1) と、Instructor および Department エンティティ間のゼロ対多また一対多リレーションシップの線 (0..1 対 \*) があります。</span><span class="sxs-lookup"><span data-stu-id="91154-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="91154-331">テスト データを使ってデータベースをシードする</span><span class="sxs-lookup"><span data-stu-id="91154-331">Seed database with test data</span></span>

<span data-ttu-id="91154-332">*Data/DbInitializer.cs* ファイルのコードを以下のコードに置き換えて、作成した新しいエンティティのシード データを提供します。</span><span class="sxs-lookup"><span data-stu-id="91154-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="91154-333">最初のチュートリアルの説明のとおり、このコードのほとんどでは単に新しいエンティティ オブジェクトを作成し、テストで必要な場合にサンプル データをプロパティに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="91154-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="91154-334">多対多リレーションシップがどのように処理されているかに注目してください。このコードでは、`Enrollments` および `CourseAssignment` 結合エンティティ セットでエンティティを作成して、リレーションシップを作成しています。</span><span class="sxs-lookup"><span data-stu-id="91154-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="91154-335">移行を追加する</span><span class="sxs-lookup"><span data-stu-id="91154-335">Add a migration</span></span>

<span data-ttu-id="91154-336">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="91154-336">Save your changes and build the project.</span></span> <span data-ttu-id="91154-337">プロジェクト フォルダーでコマンド ウィンドウを開いて、次のように `migrations add` コマンドを入力します (update-database コマンドはまだ入力しないでください)。</span><span class="sxs-lookup"><span data-stu-id="91154-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="91154-338">考えられるデータ損失に関する警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="91154-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="91154-339">この時点で `database update` コマンドを実行しようとした場合は (まだ実行しないでください)、以下のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="91154-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="91154-340">ALTER TABLE ステートメントは FOREIGN KEY 制約 "FK_dbo.Course_dbo.Department_DepartmentID" と競合しています。</span><span class="sxs-lookup"><span data-stu-id="91154-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="91154-341">競合が発生したのは、データベース "ContosoUniversity"、テーブル "dbo.Department"、列 'DepartmentID' です。</span><span class="sxs-lookup"><span data-stu-id="91154-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="91154-342">場合によっては、既存のデータで移行を実行するときに、外部キー制約を満たすためにデータベースにスタブ データを挿入する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91154-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="91154-343">`Up` メソッドの生成されたコードでは、Course テーブルに null 非許容の DepartmentID 外部キーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="91154-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="91154-344">コードの実行時に Course テーブルに既に行がある場合、`AddColumn` 操作は失敗します。これは、SQL Server では、null にできない列に配置される値が認識されないためです。</span><span class="sxs-lookup"><span data-stu-id="91154-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="91154-345">このチュートリアルでは、新しいデータベースで移行を実行しますが、運用アプリケーションでは、移行時に既存のデータを処理する必要があるため、以下の手順ではその方法例を示します。</span><span class="sxs-lookup"><span data-stu-id="91154-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="91154-346">既存のデータでこの移行を行うには、コードを変更して、新しい列に既定値を設定し、"Temp" という名前のスタブ学科を作成して、既定の学科として機能するようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="91154-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="91154-347">その結果、既存の Course 行が、`Up` メソッドの実行後に "Temp" 学科に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="91154-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="91154-348">*{timestamp}_ComplexDataModel.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="91154-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="91154-349">DepartmentID 列を Course テーブルに追加するコードの行をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="91154-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="91154-350">Department テーブルを作成するコードの後に、次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="91154-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="91154-351">運用アプリケーションでは、コードまたはスクリプトを記述して、Department 行を追加し、Course 行を新しい Department 行に関連付けます。</span><span class="sxs-lookup"><span data-stu-id="91154-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="91154-352">これで、Course.DepartmentID 列の既定値や "Temp" 学科は不要になります。</span><span class="sxs-lookup"><span data-stu-id="91154-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="91154-353">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="91154-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="91154-354">接続文字列を変更する</span><span class="sxs-lookup"><span data-stu-id="91154-354">Change the connection string</span></span>

<span data-ttu-id="91154-355">これで、新しいエンティティのシード データを空のデータベースに追加する `DbInitializer` クラスの新しいコードが準備できました。</span><span class="sxs-lookup"><span data-stu-id="91154-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="91154-356">EF で新しい空のデータベースを作成するには、*appsettings.json* の接続文字列のデータベース名を ContosoUniversity3 に変更するか、使用しているコンピューターではまだ使用していない別の名前に変更します。</span><span class="sxs-lookup"><span data-stu-id="91154-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="91154-357">変更内容を *appsettings.json* に保存します。</span><span class="sxs-lookup"><span data-stu-id="91154-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="91154-358">データベース名を変更する代わりに、データベースを削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="91154-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="91154-359">**SQL Server オブジェクト エクスプローラー** (SSOX) または `database drop` CLI コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="91154-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="91154-360">データベースを更新する</span><span class="sxs-lookup"><span data-stu-id="91154-360">Update the database</span></span>

<span data-ttu-id="91154-361">データベース名を変更またはデータベースを削除した後、コマンド ウィンドウで `database update` コマンドを実行して、移行を実行します。</span><span class="sxs-lookup"><span data-stu-id="91154-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="91154-362">アプリを実行すると、`DbInitializer.Initialize` メソッドが実行され、新しいデータベースが設定されます。</span><span class="sxs-lookup"><span data-stu-id="91154-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="91154-363">前の手順で行ったように SSOX でデータベースを開き、**Tables** ノードを展開して、テーブルがすべて作成されたことを確認します</span><span class="sxs-lookup"><span data-stu-id="91154-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="91154-364">(前に開いた SSOX がそのままの状態の場合は、 **[更新]** ボタンをクリックします)。</span><span class="sxs-lookup"><span data-stu-id="91154-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![SSOX のテーブル](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="91154-366">アプリを実行して、データベースをシードする初期化子コードをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="91154-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="91154-367">**CourseAssignment** テーブルを右クリックして、 **[データの表示]** を選択し、テーブルにデータがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="91154-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX の CourseAssignment データ](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="91154-369">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="91154-369">Get the code</span></span>

[<span data-ttu-id="91154-370">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="91154-370">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="91154-371">次の手順</span><span class="sxs-lookup"><span data-stu-id="91154-371">Next steps</span></span>

<span data-ttu-id="91154-372">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="91154-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91154-373">データ モデルをカスタマイズした</span><span class="sxs-lookup"><span data-stu-id="91154-373">Customized the Data model</span></span>
> * <span data-ttu-id="91154-374">Student エンティティに変更を加えた</span><span class="sxs-lookup"><span data-stu-id="91154-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="91154-375">Instructor エンティティを作成した</span><span class="sxs-lookup"><span data-stu-id="91154-375">Created Instructor entity</span></span>
> * <span data-ttu-id="91154-376">OfficeAssignment エンティティを作成した</span><span class="sxs-lookup"><span data-stu-id="91154-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="91154-377">Course エンティティを変更した</span><span class="sxs-lookup"><span data-stu-id="91154-377">Modified Course entity</span></span>
> * <span data-ttu-id="91154-378">Department エンティティを作成した</span><span class="sxs-lookup"><span data-stu-id="91154-378">Created Department entity</span></span>
> * <span data-ttu-id="91154-379">Enrollment エンティティを変更した</span><span class="sxs-lookup"><span data-stu-id="91154-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="91154-380">データベース コンテキストを更新した</span><span class="sxs-lookup"><span data-stu-id="91154-380">Updated the database context</span></span>
> * <span data-ttu-id="91154-381">テスト データを使ってデータベースをシードした</span><span class="sxs-lookup"><span data-stu-id="91154-381">Seeded database with test data</span></span>
> * <span data-ttu-id="91154-382">移行を追加した</span><span class="sxs-lookup"><span data-stu-id="91154-382">Added a migration</span></span>
> * <span data-ttu-id="91154-383">接続文字列を変更した</span><span class="sxs-lookup"><span data-stu-id="91154-383">Changed the connection string</span></span>
> * <span data-ttu-id="91154-384">データベースを更新した</span><span class="sxs-lookup"><span data-stu-id="91154-384">Updated the database</span></span>

<span data-ttu-id="91154-385">関連データにアクセスする方法について確認するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="91154-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="91154-386">次へ: 関連データにアクセスする</span><span class="sxs-lookup"><span data-stu-id="91154-386">Next: Access related data</span></span>](read-related-data.md)
