---
title: ASP.NET Core の Razor ページと EF Core - データ モデル - 5/8
author: rick-anderson
description: このチュートリアルでは、エンティティとリレーションシップをさらに追加し、書式設定、検証、マッピングの規則を指定してデータ モデルをカスタマイズします。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: f995f3f74da4910de518af875eb89349a8457573
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813711"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="d0aeb-103">ASP.NET Core の Razor ページと EF Core - データ モデル - 5/8</span><span class="sxs-lookup"><span data-stu-id="d0aeb-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="d0aeb-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0aeb-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="d0aeb-105">前のチュートリアルでは、3 つのエンティティで構成された基本的なデータ モデルを使用して作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="d0aeb-106">このチュートリアルでは、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-106">In this tutorial:</span></span>

* <span data-ttu-id="d0aeb-107">エンティティとリレーションシップをさらに追加する。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="d0aeb-108">書式設定、検証、データベース マッピングの規則を指定して、データ モデルをカスタマイズする。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="d0aeb-109">完成したデータ モデルのエンティティ クラスは、次の図のようになります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-109">The entity classes for the completed data model is shown in the following illustration:</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

<span data-ttu-id="d0aeb-111">解決できない問題が発生した場合は、[完成したアプリ](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)をダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-111">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="d0aeb-112">属性を使用してデータ モデルをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d0aeb-112">Customize the data model with attributes</span></span>

<span data-ttu-id="d0aeb-113">このセクションでは、属性を使用してデータ モデルをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-113">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="d0aeb-114">DataType 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-114">The DataType attribute</span></span>

<span data-ttu-id="d0aeb-115">学生のページには現在、登録日の時刻が表示されています。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-115">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="d0aeb-116">通常、日付フィールドには日付のみが表示され、時刻は表示されません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-116">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="d0aeb-117">以下の強調表示されているコードを使用して、*Models/Student.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-117">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="d0aeb-118">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 属性では、データベースの組み込み型よりも具体的なデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-118">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="d0aeb-119">ここでは、日付と時刻ではなく、日付のみを表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-119">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="d0aeb-120">[DataType 列挙型](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くのデータ型のために用意されています。また、`DataType` 属性を使用して、アプリで型固有の機能を自動的に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-120">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="d0aeb-121">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-121">For example:</span></span>

* <span data-ttu-id="d0aeb-122">`mailto:` リンクは `DataType.EmailAddress` に対して自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-122">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="d0aeb-123">ほとんどのブラウザーでは、`DataType.Date` に日付セレクターが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-123">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="d0aeb-124">`DataType` 属性は、HTML 5 ブラウザーが使用する HTML 5 `data-` (データ ダッシュと読む) 属性を出力します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-124">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="d0aeb-125">`DataType` 属性では検証は提供されません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-125">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="d0aeb-126">`DataType.Date` は、表示される日付の書式を指定しません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-126">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="d0aeb-127">既定で、日付フィールドはサーバーの [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) に基づき、既定の書式に従って表示されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-127">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="d0aeb-128">`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-128">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="d0aeb-129">`ApplyFormatInEditMode` 設定では、書式設定を編集 UI にも適用する必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-129">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="d0aeb-130">一部のフィールドでは `ApplyFormatInEditMode` を使用できません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-130">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="d0aeb-131">たとえば、通貨記号は一般的に編集テキスト ボックスには表示できません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-131">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="d0aeb-132">`DisplayFormat` 属性は単独で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-132">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="d0aeb-133">一般的には、`DataType` 属性を `DisplayFormat` 属性と一緒に使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-133">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="d0aeb-134">`DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="d0aeb-135">`DataType` 属性には、`DisplayFormat` では得られない以下のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-135">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="d0aeb-136">ブラウザーで HTML5 機能を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-136">The browser can enable HTML5 features.</span></span> <span data-ttu-id="d0aeb-137">たとえば、カレンダー コントロール、ロケールに適した通貨記号、メール リンク、クライアント側の入力検証などを表示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-137">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="d0aeb-138">既定では、ブラウザーで、ロケールに基づいて正しい書式を使用してデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-138">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="d0aeb-139">詳細については、[\<入力> タグ ヘルパーに関するドキュメント](xref:mvc/views/working-with-forms#the-input-tag-helper)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-139">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="d0aeb-140">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-140">Run the app.</span></span> <span data-ttu-id="d0aeb-141">Students インデックス ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-141">Navigate to the Students Index page.</span></span> <span data-ttu-id="d0aeb-142">時刻は表示されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-142">Times are no longer displayed.</span></span> <span data-ttu-id="d0aeb-143">`Student` モデルを使用するすべてのビューに、時刻なしの日付が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-143">Every view that uses the `Student` model displays the date without time.</span></span>

![時刻なしの日付が表示されている Students インデックス ページ](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="d0aeb-145">StringLength 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-145">The StringLength attribute</span></span>

<span data-ttu-id="d0aeb-146">データ検証規則と検証エラー メッセージは、属性を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="d0aeb-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 属性では、データ フィールドで使用できる最小文字長と最大文字長を指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="d0aeb-148">また、`StringLength` 属性ではクライアント側とサーバー側の検証も提供されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-148">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="d0aeb-149">最小値は、データベース スキーマに影響しません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-149">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="d0aeb-150">`Student` モデルを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-150">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="d0aeb-151">上のコードでは、名前で使用可能な文字数を 50 に制限します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-151">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="d0aeb-152">`StringLength` 属性では、ユーザーが名前に空白を入力しないようにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="d0aeb-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 属性は、入力に制限を適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="d0aeb-154">たとえば、次のコードでは、最初の文字を大文字にし、残りの文字をアルファベット順にすることを要求します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="d0aeb-155">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-155">Run the app:</span></span>

* <span data-ttu-id="d0aeb-156">Students のページに移動します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-156">Navigate to the Students page.</span></span>
* <span data-ttu-id="d0aeb-157">**[新規作成]** を選択し、50 文字を超える名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-157">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="d0aeb-158">**[作成]** を選択すると、クライアント側の検証でエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-158">Select **Create**, client-side validation shows an error message.</span></span>

![文字列長エラーが表示されている Students インデックス ページ](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="d0aeb-160">**SQL Server オブジェクト エクスプローラー** (SSOX) で、**Student** テーブルをダブルクリックして、Student テーブル デザイナーを開きます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-160">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移行前の SSOX の Students テーブル](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="d0aeb-162">上の図には `Student` テーブルのスキーマが表示されています。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-162">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="d0aeb-163">DB では移行が実行されていないため、名前フィールドに `nvarchar(MAX)` 型があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-163">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="d0aeb-164">このチュートリアルの後半で移行が実行されたときに、名前フィールドが `nvarchar(50)` になります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-164">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="d0aeb-165">Column 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-165">The Column attribute</span></span>

<span data-ttu-id="d0aeb-166">属性で、データベースへのクラスとプロパティのマッピング方法を制御することができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-166">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="d0aeb-167">このセクションでは、`Column` 属性を使用して、`FirstMidName` プロパティの名前を DB の "FirstName" にマッピングします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-167">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="d0aeb-168">DB が作成されたときに、列名でモデルのプロパティ名が使用されます (`Column` 属性が使用されている場合を除く)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-168">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="d0aeb-169">`Student` モデルでは名フィールドに対して `FirstMidName` が使用されます。これは、フィールドにミドル ネームも含まれている場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-169">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="d0aeb-170">以下の強調表示されているコードを使用して、*Student.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-170">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="d0aeb-171">前述の変更に伴い、アプリの `Student.FirstMidName` は `Student` テーブルの `FirstName` 列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-171">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="d0aeb-172">`Column` 属性を追加すると、`SchoolContext` をサポートするモデルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="d0aeb-173">`SchoolContext` をサポートするモデルはデータベースと一致しなくなります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="d0aeb-174">移行を適用する前にアプリを実行した場合は、次の例外が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-174">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="d0aeb-175">DB を更新するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-175">To update the DB:</span></span>

* <span data-ttu-id="d0aeb-176">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-176">Build the project.</span></span>
* <span data-ttu-id="d0aeb-177">プロジェクト フォルダーでコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-177">Open a command window in the project folder.</span></span> <span data-ttu-id="d0aeb-178">以下のコマンドを入力し、新しい移行を作成して DB を更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-178">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d0aeb-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0aeb-179">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d0aeb-180">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d0aeb-180">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="d0aeb-181">`migrations add ColumnFirstName` コマンドでは、以下の警告メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-181">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="d0aeb-182">名前フィールドは現在、50 文字に制限されているため、警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-182">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="d0aeb-183">DB の名前が 50 文字を超えた場合、51 番目から最後までの文字が失われます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-183">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="d0aeb-184">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-184">Test the app.</span></span>

<span data-ttu-id="d0aeb-185">SSOX で Student テーブルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-185">Open the Student table in SSOX:</span></span>

![移行後の SSOX の Students テーブル](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="d0aeb-187">移行が適用される前の名前列の型は [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) でした。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-187">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="d0aeb-188">現在の名前列は `nvarchar(50)` です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-188">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="d0aeb-189">列名は `FirstMidName` から `FirstName` に変わりました。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-189">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="d0aeb-190">次のセクションでは、いくつかのステージでアプリをビルドします。その場合、コンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-190">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="d0aeb-191">手順では、アプリをビルドするタイミングを指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-191">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="d0aeb-192">Student エンティティの更新</span><span class="sxs-lookup"><span data-stu-id="d0aeb-192">Student entity update</span></span>

![Student エンティティ](complex-data-model/_static/student-entity.png)

<span data-ttu-id="d0aeb-194">以下のコードを使用して、*Models/Student.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-194">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="d0aeb-195">Required 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-195">The Required attribute</span></span>

<span data-ttu-id="d0aeb-196">`Required` 属性では、名前プロパティの必須フィールドを作成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-196">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="d0aeb-197">値の型 (`DateTime`、`int`、`double` など) などの null 非許容型では `Required` 属性は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-197">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="d0aeb-198">null にできない型は自動的に必須フィールドとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-198">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="d0aeb-199">`Required` 属性は、`StringLength` 属性の最小長パラメーターに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-199">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="d0aeb-200">Display 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-200">The Display attribute</span></span>

<span data-ttu-id="d0aeb-201">`Display` 属性では、テキスト ボックスのキャプションを "First Name"、"Last Name"、"Full Name"、"Enrollment Date" にするよう指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-201">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="d0aeb-202">既定のキャプションには、"Lastname" のように、単語を区切るスペースはありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-202">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="d0aeb-203">FullName 集計プロパティ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-203">The FullName calculated property</span></span>

<span data-ttu-id="d0aeb-204">`FullName` は集計プロパティであり、2 つの別のプロパティを連結して作成される値を返します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-204">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="d0aeb-205">`FullName` を設定することはできません。get アクセサーのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-205">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="d0aeb-206">データベースには `FullName` 列は作成されません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-206">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="d0aeb-207">Instructor エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-207">Create the Instructor Entity</span></span>

![Instructor エンティティ](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="d0aeb-209">以下のコードを使用して、*Models/Instructor.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-209">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="d0aeb-210">複数の属性を 1 行に配置することができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-210">Multiple attributes can be on one line.</span></span> <span data-ttu-id="d0aeb-211">`HireDate` 属性は次のように記述できます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-211">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="d0aeb-212">CourseAssignments と OfficeAssignment ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-212">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="d0aeb-213">`CourseAssignments` と `OfficeAssignment` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-213">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="d0aeb-214">講師は任意の数のコースを担当できるため、`CourseAssignments` はコレクションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-214">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="d0aeb-215">ナビゲーション プロパティに複数のエンティティが保持されている場合:</span><span class="sxs-lookup"><span data-stu-id="d0aeb-215">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="d0aeb-216">エンティティを追加、削除、更新できるリスト型である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-216">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="d0aeb-217">ナビゲーション プロパティの型には次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-217">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="d0aeb-218">`ICollection<T>` が指定されている場合は、EF Core で `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-218">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="d0aeb-219">`CourseAssignment` エンティティについては、多対多リレーションシップのセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-219">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="d0aeb-220">Contoso University のビジネス ルールには、講師は 1 つのオフィスのみを持つことができると示されています。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-220">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="d0aeb-221">`OfficeAssignment` プロパティでは単一の `OfficeAssignment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-221">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="d0aeb-222">オフィスが割り当てられていない場合、`OfficeAssignment` は null です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-222">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="d0aeb-223">OfficeAssignment エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-223">Create the OfficeAssignment entity</span></span>

![OfficeAssignment エンティティ](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="d0aeb-225">以下のコードを使用して、*Models/OfficeAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-225">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="d0aeb-226">Key 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-226">The Key attribute</span></span>

<span data-ttu-id="d0aeb-227">`[Key]` 属性は、プロパティ名が classnameID や ID 以外である場合に、主キー (PK) としてプロパティを識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-227">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="d0aeb-228">`Instructor` エンティティと `OfficeAssignment` エンティティの間には一対ゼロまたは一対一のリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-228">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="d0aeb-229">オフィスが割り当てられている講師についてのみ、オフィス割り当てが存在します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-229">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="d0aeb-230">`OfficeAssignment` PK は、`Instructor` エンティティに対する外部キー (FK) でもあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-230">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="d0aeb-231">EF Core では、以下の理由で、`OfficeAssignment` の PK として `InstructorID` を自動的に認識することはできません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-231">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="d0aeb-232">`InstructorID` は、ID や classnameID の名前付け規則には従っていません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-232">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="d0aeb-233">したがって、`Key` 属性は PK として `InstructorID` を識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="d0aeb-234">列は依存リレーションシップに対するものであるため、既定では EF Core はキーを非データベース生成として扱います。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="d0aeb-235">Instructor ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-235">The Instructor navigation property</span></span>

<span data-ttu-id="d0aeb-236">`Instructor` エンティティの `OfficeAssignment` ナビゲーション プロパティは、以下の理由で null 許容型となります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-236">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="d0aeb-237">参照型 (クラスが null 許容型である)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-237">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="d0aeb-238">講師にオフィスが割り当てられていない可能性がある。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-238">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="d0aeb-239">`OfficeAssignment` エンティティには、以下の理由で null 非許容の `Instructor` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-239">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="d0aeb-240">`InstructorID` は null 非許容です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-240">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="d0aeb-241">オフィス割り当ては講師なしでは存在できません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="d0aeb-242">`Instructor` エンティティに関連する `OfficeAssignment` エンティティがある場合、各エンティティにはそのナビゲーション プロパティの別のエンティティへの参照があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="d0aeb-243">`[Required]` 属性を `Instructor` ナビゲーション プロパティに適用することができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-243">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="d0aeb-244">上のコードは、関連する講師が存在する必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-244">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="d0aeb-245">`InstructorID` 外部キー (PK でもある) が null 非許容であるため、上のコードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-245">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="d0aeb-246">Course エンティティを変更する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-246">Modify the Course Entity</span></span>

![Course エンティティ](complex-data-model/_static/course-entity.png)

<span data-ttu-id="d0aeb-248">以下のコードを使用して、*Models/Course.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-248">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="d0aeb-249">`Course` エンティティには外部キー (FK) プロパティ `DepartmentID` があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-249">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="d0aeb-250">`DepartmentID` は関連する `Department` エンティティを指します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-250">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="d0aeb-251">`Course` エンティティには `Department` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-251">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="d0aeb-252">EF Core では、モデルに関連エンティティのナビゲーション プロパティがある場合、データ モデルの FK プロパティは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-252">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="d0aeb-253">EF Core は、必要に応じて、データベースで自動的に FK を作成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-253">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="d0aeb-254">EF Core は、自動的に作成された FK に対して、[シャドウ プロパティ](/ef/core/modeling/shadow-properties)を作成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-254">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="d0aeb-255">データ モデルに FK がある場合は、更新をより簡単かつ効率的に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-255">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="d0aeb-256">たとえば、FK プロパティ `DepartmentID` が含まれて*いない* モデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-256">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="d0aeb-257">Course エンティティが編集用にフェッチされた場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-257">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="d0aeb-258">`Department` エンティティは、明示的に読み込まれない場合、null となります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-258">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="d0aeb-259">Course エンティティを更新するには、`Department` エンティティを最初にフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-259">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="d0aeb-260">FK エンティティ `DepartmentID` がデータ モデルに含まれている場合は、更新前に `Department` エンティティをフェッチする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-260">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="d0aeb-261">DatabaseGenerated 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-261">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="d0aeb-262">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 属性では、PK をデータベースで生成するのではなく、アプリケーションで提供するように指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-262">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="d0aeb-263">既定では、EF Core は、PK 値が DB で生成されることを前提とします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-263">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="d0aeb-264">通常は、PK 値を DB で生成するのが最適です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-264">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="d0aeb-265">`Course` エンティティの場合、PK はユーザーが指定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-265">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="d0aeb-266">たとえば、数学科の場合は 1000 シリーズ、英文科の場合は 2000 シリーズなどのコース番号となります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-266">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="d0aeb-267">`DatabaseGenerated` 属性は、既定値を生成する場合にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-267">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="d0aeb-268">たとえば、DB では、行が作成または更新された日付を記録するための日付フィールドを自動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-268">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="d0aeb-269">詳細については、「[生成される値](/ef/core/modeling/generated-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-269">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="d0aeb-270">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-270">Foreign key and navigation properties</span></span>

<span data-ttu-id="d0aeb-271">`Course` エンティティの外部キー (FK) プロパティとナビゲーション プロパティには、以下のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-271">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="d0aeb-272">コースが 1 つの学科に割り当てられています。したがって、`DepartmentID` FK と `Department` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-272">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="d0aeb-273">コースには任意の数の学生が登録できるため、`Enrollments` ナビゲーション プロパティはコレクションとなります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-273">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="d0aeb-274">コースは複数の講師が担当する場合があるため、`CourseAssignments` ナビゲーション プロパティはコレクションとなります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-274">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="d0aeb-275">`CourseAssignment` については、[後で](#many-to-many-relationships)説明します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-275">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="d0aeb-276">Department エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-276">Create the Department entity</span></span>

![Department エンティティ](complex-data-model/_static/department-entity.png)

<span data-ttu-id="d0aeb-278">以下のコードを使用して、*Models/Department.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-278">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="d0aeb-279">Column 属性</span><span class="sxs-lookup"><span data-stu-id="d0aeb-279">The Column attribute</span></span>

<span data-ttu-id="d0aeb-280">これまでは、`Column` 属性が列名のマッピングを変更するために使用されました。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-280">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="d0aeb-281">`Department` エンティティのコードでは、`Column` 属性は SQL データ型のマッピングを変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-281">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="d0aeb-282">`Budget` 列は、次のように、DB の SQL Server の money 型を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-282">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="d0aeb-283">通常、列マッピングは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-283">Column mapping is generally not required.</span></span> <span data-ttu-id="d0aeb-284">通常、EF Core はプロパティの CLR 型に基づいて、適切な SQL Server のデータ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-284">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="d0aeb-285">CLR `decimal` 型は SQL Server の `decimal` 型にマップされます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-285">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="d0aeb-286">`Budget` は通貨用であり、通貨には money データ型がより適しています。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-286">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="d0aeb-287">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-287">Foreign key and navigation properties</span></span>

<span data-ttu-id="d0aeb-288">FK およびナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-288">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="d0aeb-289">学科には管理者が存在する場合とそうでない場合があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-289">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="d0aeb-290">管理者は常に講師です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-290">An administrator is always an instructor.</span></span> <span data-ttu-id="d0aeb-291">したがって、`InstructorID` プロパティは `Instructor` エンティティに対する FK として含まれます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-291">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="d0aeb-292">ナビゲーション プロパティは `Administrator` という名前ですが、`Instructor` エンティティを保持します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-292">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="d0aeb-293">上のコードの疑問符 (?) は、プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-293">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="d0aeb-294">学科には複数のコースがある場合があるため、Courses ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-294">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="d0aeb-295">メモ:規則により、EF Core では null 非許容の FK と多対多リレーションシップに対して連鎖削除が有効になります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-295">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="d0aeb-296">連鎖削除では、循環連鎖削除規則が適用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-296">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="d0aeb-297">循環連鎖削除規則が適用されると、移行の追加時に例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-297">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="d0aeb-298">たとえば、`Department.InstructorID` プロパティが null 許容として定義されなかった場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-298">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="d0aeb-299">EF Core は、講師が削除されたときに学科を削除するように連鎖削除規則を構成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-299">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="d0aeb-300">講師が削除されたときに学科を削除するのは、意図した動作ではありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-300">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="d0aeb-301">次の fluent API で、連鎖の代わりに制限規則を設定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-301">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="d0aeb-302">上のコードでは、学科と講師のリレーションシップの連鎖削除が無効になります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-302">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="d0aeb-303">Enrollment エンティティを更新する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-303">Update the Enrollment entity</span></span>

<span data-ttu-id="d0aeb-304">1 件の登録レコードは、1 人の学生が受講する 1 つのコースに対するものです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-304">An enrollment record is for one course taken by one student.</span></span>

![Enrollment エンティティ](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="d0aeb-306">以下のコードを使用して、*Models/Enrollment.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-306">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="d0aeb-307">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-307">Foreign key and navigation properties</span></span>

<span data-ttu-id="d0aeb-308">FK プロパティとナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="d0aeb-309">登録レコードは 1 つのコースに対するものであるため、`CourseID` FK プロパティと `Course` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="d0aeb-310">登録レコードは 1 人の学生に対するものであるため、`StudentID` FK プロパティと `Student` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="d0aeb-311">多対多リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="d0aeb-312">`Student` エンティティと `Course` エンティティの間には多対多リレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="d0aeb-313">`Enrollment` エンティティは、データベースで*ペイロードがある*多対多結合テーブルとして機能します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-313">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="d0aeb-314">"ペイロードがある" とは、`Enrollment` テーブルに、結合テーブルの FK 以外に追加データが含まれていることを意味します (ここでは PK と `Grade`)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-314">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="d0aeb-315">次の図は、エンティティ図でこれらのリレーションシップがどのようになるかを示しています</span><span class="sxs-lookup"><span data-stu-id="d0aeb-315">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="d0aeb-316">(この図は、EF 6.x 用の [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) を使用して生成されたものです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-316">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="d0aeb-317">このチュートリアルでは図は作成しません)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-317">Creating the diagram isn't part of the tutorial.)</span></span>

![Student と Course の多対多リレーションシップ](complex-data-model/_static/student-course.png)

<span data-ttu-id="d0aeb-319">各リレーションシップ線の一方の端に 1 が、もう一方の端にアスタリスク (\*) があり、1 対多リレーションシップであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-319">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="d0aeb-320">`Enrollment` テーブルに成績情報が含まれていない場合、含める必要があるのは 2 つの FK (`CourseID` と `StudentID`) のみです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-320">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="d0aeb-321">ペイロードがない多対多結合テーブルは純粋結合テーブル (PJT) と呼ばれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-321">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="d0aeb-322">`Instructor` および `Course` エンティティには、純粋結合テーブルを使用する多対多リレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-322">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="d0aeb-323">メモ:EF 6.x では多対多リレーションシップの暗黙の結合テーブルがサポートされますが、EF Core ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-323">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="d0aeb-324">詳細については、「[Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)」 (EF Core 2.0 の多対多リレーションシップ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-324">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="d0aeb-325">CourseAssignment エンティティ</span><span class="sxs-lookup"><span data-stu-id="d0aeb-325">The CourseAssignment entity</span></span>

![CourseAssignment エンティティ](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="d0aeb-327">以下のコードを使用して、*Models/CourseAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-327">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="d0aeb-328">講師対コース</span><span class="sxs-lookup"><span data-stu-id="d0aeb-328">Instructor-to-Courses</span></span>

![講師対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="d0aeb-330">講師対コースの多対多リレーションシップは、</span><span class="sxs-lookup"><span data-stu-id="d0aeb-330">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="d0aeb-331">エンティティ セットで表される必要がある結合テーブルが必要です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-331">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="d0aeb-332">純粋結合テーブル (ペイロードがないテーブル) です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-332">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="d0aeb-333">結合エンティティには `EntityName1EntityName2` という名前が付けるのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-333">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="d0aeb-334">たとえば、このパターンを使用する講師対コースの結合テーブルは `CourseInstructor` となります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-334">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="d0aeb-335">ただし、リレーションシップを説明する名前を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-335">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="d0aeb-336">データ モデルは始めは単純なものであっても大きくなります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-336">Data models start out simple and grow.</span></span> <span data-ttu-id="d0aeb-337">ペイロードがない結合 (PJT) は頻繁に進化し、ペイロードが含まれるようになります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-337">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="d0aeb-338">最初にわかりやすいエンティティ名を付けておけば、結合テーブルが変更されたときに名前を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-338">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="d0aeb-339">結合エンティティでは、ビジネス ドメインに独自の自然な (場合によっては 1 単語の) 名前を指定することが理想的です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-339">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="d0aeb-340">たとえば、Books と Customers は Ratings という結合エンティティでリンクできます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-340">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="d0aeb-341">講師対コースの多対多リレーションシップの場合、`CourseAssignment` は `CourseInstructor` より優先されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-341">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="d0aeb-342">複合キー</span><span class="sxs-lookup"><span data-stu-id="d0aeb-342">Composite key</span></span>

<span data-ttu-id="d0aeb-343">FK は null 非許容です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-343">FKs are not nullable.</span></span> <span data-ttu-id="d0aeb-344">`CourseAssignment` の 2 つの FK (`InstructorID` と `CourseID`) を組み合わせて使用し、`CourseAssignment` テーブルの各行を一意に識別します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-344">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="d0aeb-345">`CourseAssignment` には専用の PK は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-345">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="d0aeb-346">`InstructorID` および `CourseID` プロパティは複合 PK として機能します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-346">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="d0aeb-347">EF Core に複合 PK を指定する唯一の方法は、*fluent API* を使用することです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-347">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="d0aeb-348">次のセクションでは、複合 PK の構成方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-348">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="d0aeb-349">複合キーでは、必ず次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-349">The composite key ensures:</span></span>

* <span data-ttu-id="d0aeb-350">1 つのコースに対して複数の行が許可される。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-350">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="d0aeb-351">1 人の講師に対して複数の行が許可される。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-351">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="d0aeb-352">同じ講師とコースに対して複数の行が許可されない。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-352">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="d0aeb-353">`Enrollment` 結合エンティティでは独自の PK を定義するため、このような重複が考えられます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-353">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="d0aeb-354">このような重複を防ぐには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-354">To prevent such duplicates:</span></span>

* <span data-ttu-id="d0aeb-355">FK フィールドに一意のインデックスを追加する。または</span><span class="sxs-lookup"><span data-stu-id="d0aeb-355">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="d0aeb-356">`CourseAssignment` と同様の複合主キーを使用して、`Enrollment` を構成する。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-356">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="d0aeb-357">詳細については、「[インデックス](/ef/core/modeling/indexes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-357">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="d0aeb-358">DB コンテキストを更新する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-358">Update the DB context</span></span>

<span data-ttu-id="d0aeb-359">次の強調表示されているコードを *Data/SchoolContext.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-359">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="d0aeb-360">上のコードでは新しいエンティティが追加され、`CourseAssignment` エンティティの複合 PK が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-360">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="d0aeb-361">属性の代わりに fluent API を使用する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-361">Fluent API alternative to attributes</span></span>

<span data-ttu-id="d0aeb-362">上のコードの `OnModelCreating` メソッドでは、*fluent API* を使用して EF Core の動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-362">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="d0aeb-363">API は "fluent" と呼ばれます。これは、多くの場合、一連のメソッド呼び出しを単一のステートメントにまとめて使用されるためです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-363">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="d0aeb-364">[次のコード](/ef/core/modeling/#use-fluent-api-to-configure-a-model)は fluent API の例です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-364">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="d0aeb-365">このチュートリアルでは、属性で実行できない DB マッピングの場合にのみ、fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-365">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="d0aeb-366">ただし、fluent API では、属性で実行できる書式設定、検証、マッピング規則のほとんどを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-366">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="d0aeb-367">`MinimumLength` などの一部の属性は fluent API で適用できません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-367">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="d0aeb-368">`MinimumLength` ではスキーマを変更せず、最小長の検証規則のみを適用します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-368">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="d0aeb-369">一部の開発者は fluent API のみを使用することを選ぶため、エンティティ クラスを "クリーン" な状態に保つことができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-369">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="d0aeb-370">属性と fluent API を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-370">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="d0aeb-371">(複合 PK を指定して) fluent API でのみ実行できる構成がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-371">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="d0aeb-372">属性 (`MinimumLength`) でのみ実行できる構成もいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-372">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="d0aeb-373">次のように、fluent API または属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-373">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="d0aeb-374">これら 2 つの方法のいずれかを選択する。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-374">Choose one of these two approaches.</span></span>
* <span data-ttu-id="d0aeb-375">できるだけ一貫性を保つために選択した方法を使用する。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-375">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="d0aeb-376">このチュートリアルで使用する属性のいくつかは、次の用途に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-376">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="d0aeb-377">検証のみ (`MinimumLength` など)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-377">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="d0aeb-378">EF Core 構成のみ (`HasKey` など)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-378">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="d0aeb-379">検証と EF Core の構成 (`[StringLength(50)]` など)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-379">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="d0aeb-380">属性と fluent API の詳細については、「[構成の方法](/ef/core/modeling/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-380">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="d0aeb-381">リレーションシップを示すエンティティ図</span><span class="sxs-lookup"><span data-stu-id="d0aeb-381">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="d0aeb-382">次の図では、完成した School モデルに対して EF Power Tools で作成される図を示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-382">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

<span data-ttu-id="d0aeb-384">上の図には以下が示されています。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-384">The preceding diagram shows:</span></span>

* <span data-ttu-id="d0aeb-385">いくつかの一対多リレーションシップの線 (1 対 \*)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-385">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="d0aeb-386">`Instructor` エンティティと `OfficeAssignment` エンティティの間の一対ゼロまたは一対一リレーションシップの線 (1 対 0..1)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-386">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="d0aeb-387">`Instructor` エンティティと `Department` エンティティの間のゼロ対一またはゼロ対多リレーションシップの線 (1 対 0..1)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-387">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="d0aeb-388">テスト データで DB をシードする</span><span class="sxs-lookup"><span data-stu-id="d0aeb-388">Seed the DB with Test Data</span></span>

<span data-ttu-id="d0aeb-389">*Data/DbInitializer.cs* のコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-389">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="d0aeb-390">上のコードでは、新しいエンティティのシード データが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-390">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="d0aeb-391">このコードのほとんどで新しいエンティティ オブジェクトが作成され、サンプル データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-391">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="d0aeb-392">サンプル データはテストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-392">The sample data is used for testing.</span></span> <span data-ttu-id="d0aeb-393">多対多結合テーブルがシードされる方法の例については、`Enrollments` と `CourseAssignments` を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-393">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="d0aeb-394">移行を追加する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-394">Add a migration</span></span>

<span data-ttu-id="d0aeb-395">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-395">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d0aeb-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0aeb-396">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d0aeb-397">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d0aeb-397">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="d0aeb-398">上のコマンドは、考えられるデータ損失に関する警告を表示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-398">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="d0aeb-399">`database update` コマンドを実行すると、次のエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-399">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="d0aeb-400">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-400">Apply the migration</span></span>

<span data-ttu-id="d0aeb-401">既存のデータベースができたので、将来の変更を適用する方法について検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-401">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="d0aeb-402">このチュートリアルでは、2 つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-402">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="d0aeb-403">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-403">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="d0aeb-404">[移行を既存のデータベースに適用する](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-404">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="d0aeb-405">この方法はより複雑で時間がかかりますが、実際の運用環境では推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-405">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="d0aeb-406">**注**:これは、チュートリアルのオプションのセクションです。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-406">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="d0aeb-407">削除と再作成の手順を行い、このセクションはスキップしてもかまいません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-407">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="d0aeb-408">このセクションの手順に従う場合は、削除と再作成の手順を行わないでください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-408">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d0aeb-409">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-409">Drop and re-create the database</span></span>

<span data-ttu-id="d0aeb-410">更新された `DbInitializer` のコードでは、新しいエンティティのシード データを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-410">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="d0aeb-411">EF Core に新しい DB を強制的に作成させるには、DB を削除して更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-411">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d0aeb-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0aeb-412">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d0aeb-413">**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-413">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="d0aeb-414">PMC から `Get-Help about_EntityFrameworkCore` を実行してヘルプ情報を入手します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-414">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d0aeb-415">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d0aeb-415">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d0aeb-416">コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-416">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="d0aeb-417">プロジェクト フォルダーには *Startup.cs* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-417">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="d0aeb-418">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-418">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="d0aeb-419">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-419">Run the app.</span></span> <span data-ttu-id="d0aeb-420">アプリを実行すると `DbInitializer.Initialize` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-420">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="d0aeb-421">`DbInitializer.Initialize` は新しい DB を設定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-421">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="d0aeb-422">SSOX で DB を開きます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-422">Open the DB in SSOX:</span></span>

* <span data-ttu-id="d0aeb-423">SSOX が既に開いている場合は、 **[更新]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-423">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="d0aeb-424">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-424">Expand the **Tables** node.</span></span> <span data-ttu-id="d0aeb-425">作成されたテーブルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-425">The created tables are displayed.</span></span>

![SSOX のテーブル](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="d0aeb-427">**CourseAssignment** テーブルを確認します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-427">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="d0aeb-428">**CourseAssignment** テーブルを右クリックして、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-428">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="d0aeb-429">**CourseAssignment** テーブルにデータが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-429">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX の CourseAssignment データ](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="d0aeb-431">移行を既存のデータベースに適用する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-431">Apply the migration to the existing database</span></span>

<span data-ttu-id="d0aeb-432">このセクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-432">This section is optional.</span></span> <span data-ttu-id="d0aeb-433">以下の手順は、前の「[データベースを削除して再作成する](#drop)」セクションをスキップした場合にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-433">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="d0aeb-434">既存のデータで移行が実行されている場合、既存のデータでは満たされない FK 制約が存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="d0aeb-435">運用データを使用する場合は、既存のデータを移行するための手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="d0aeb-436">このセクションでは、FK 制約違反の修正例を示します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="d0aeb-437">これらのコードをバックアップせずに変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="d0aeb-438">前のセクションを完了し、データベースを更新した場合は、これらのコードを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-438">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="d0aeb-439">*{timestamp}_ComplexDataModel.cs* ファイルには次のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="d0aeb-440">上のコードでは、null 非許容の `DepartmentID` FK が `Course` テーブルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="d0aeb-441">前のチュートリアルの DB には `Course` の行が含まれるため、テーブルを移行して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-441">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="d0aeb-442">既存のデータを使用して `ComplexDataModel` の移行を実行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="d0aeb-443">コードを変更して、新しい列 (`DepartmentID`) に既定値を設定します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="d0aeb-444">"Temp" という名前の偽の学科を作成し、既定の学科として機能するようにします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="d0aeb-445">外部キー制約を修正する</span><span class="sxs-lookup"><span data-stu-id="d0aeb-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="d0aeb-446">`ComplexDataModel` クラスの `Up` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-446">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="d0aeb-447">*{timestamp}_ComplexDataModel.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="d0aeb-448">`DepartmentID` 列を `Course` テーブルに追加するコードの行をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="d0aeb-449">次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-449">Add the following highlighted code.</span></span> <span data-ttu-id="d0aeb-450">新しいコードが `.CreateTable( name: "Department"` ブロックの後に配置されます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="d0aeb-451">前述の変更に伴い、既存の `Course` 行が、`ComplexDataModel` `Up` メソッドの実行後に "Temp" 学科に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-451">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="d0aeb-452">運用アプリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-452">A production app would:</span></span>

* <span data-ttu-id="d0aeb-453">コードまたはスクリプトを組み込み、`Department` 行と関連する `Course` 行を新しい `Department` 行に追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-453">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="d0aeb-454">`Course.DepartmentID` の既定値や "Temp" 学科は使用しません。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-454">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="d0aeb-455">次のチュートリアルでは関連するデータについて説明します。</span><span class="sxs-lookup"><span data-stu-id="d0aeb-455">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0aeb-456">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d0aeb-456">Additional resources</span></span>

* [<span data-ttu-id="d0aeb-457">このチュートリアルの YouTube バージョン (パート 1)</span><span class="sxs-lookup"><span data-stu-id="d0aeb-457">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="d0aeb-458">このチュートリアルの YouTube バージョン (パート 2)</span><span class="sxs-lookup"><span data-stu-id="d0aeb-458">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> <span data-ttu-id="d0aeb-459">[前へ](xref:data/ef-rp/migrations)
> [次へ](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="d0aeb-459">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>
