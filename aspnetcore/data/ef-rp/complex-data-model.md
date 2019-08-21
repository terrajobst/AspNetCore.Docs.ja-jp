---
title: ASP.NET Core の Razor ページと EF Core - データ モデル - 5/8
author: rick-anderson
description: このチュートリアルでは、エンティティとリレーションシップをさらに追加し、書式設定、検証、マッピングの規則を指定してデータ モデルをカスタマイズします。
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 96e4acf0d6c9c079ebee32fc2f9951fdd668931b
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914968"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="e13fe-103">ASP.NET Core の Razor ページと EF Core - データ モデル - 5/8</span><span class="sxs-lookup"><span data-stu-id="e13fe-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="e13fe-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e13fe-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e13fe-105">前のチュートリアルでは、3 つのエンティティで構成された基本的なデータ モデルを使用して作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="e13fe-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="e13fe-106">このチュートリアルでは、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="e13fe-106">In this tutorial:</span></span>

* <span data-ttu-id="e13fe-107">エンティティとリレーションシップをさらに追加する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="e13fe-108">書式設定、検証、データベース マッピングの規則を指定して、データ モデルをカスタマイズする。</span><span class="sxs-lookup"><span data-stu-id="e13fe-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="e13fe-109">完成したデータ モデルは、次の図のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-109">The completed data model is shown in the following illustration:</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="e13fe-111">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-111">The Student entity</span></span>

![Student エンティティ](complex-data-model/_static/student-entity.png)

<span data-ttu-id="e13fe-113">*Models/Student.cs* のコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="e13fe-114">前のコードでは、`FullName` プロパティが追加され、既存のプロパティに次の属性が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="e13fe-115">FullName 集計プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-115">The FullName calculated property</span></span>

<span data-ttu-id="e13fe-116">`FullName` は集計プロパティであり、2 つの別のプロパティを連結して作成される値を返します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="e13fe-117">`FullName` を設定することはできないので、get アクセサーのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="e13fe-118">データベースには `FullName` 列は作成されません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="e13fe-119">DataType 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="e13fe-120">学生の登録日について、日付のみが関係しますが、現在はすべての Web ページに日付と共に時刻が表示されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="e13fe-121">データ注釈属性を使用すれば、1 つのコードを変更するだけで、データが表示されるすべてのページの表示形式を修正できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="e13fe-122">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 属性では、データベースの組み込み型よりも具体的なデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="e13fe-123">ここでは、日付と時刻ではなく、日付のみを表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="e13fe-124">[DataType 列挙型](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くのデータ型のために用意されています。また、`DataType` 属性を使用して、アプリで型固有の機能を自動的に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="e13fe-125">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-125">For example:</span></span>

* <span data-ttu-id="e13fe-126">`mailto:` リンクは `DataType.EmailAddress` に対して自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="e13fe-127">ほとんどのブラウザーでは、`DataType.Date` に日付セレクターが提供されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="e13fe-128">`DataType` 属性では、HTML 5 の `data-` (データ ダッシュと読みます) 属性が出力されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="e13fe-129">`DataType` 属性では検証は提供されません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="e13fe-130">DisplayFormat 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="e13fe-131">`DataType.Date` は、表示される日付の書式を指定しません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="e13fe-132">既定で、日付フィールドはサーバーの [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) に基づき、既定の書式に従って表示されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="e13fe-133">`DisplayFormat` 属性は、日付の形式を明示的に指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="e13fe-134">`ApplyFormatInEditMode` 設定では、書式設定を編集 UI にも適用する必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="e13fe-135">一部のフィールドでは `ApplyFormatInEditMode` を使用できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="e13fe-136">たとえば、通貨記号は一般的に編集テキスト ボックスには表示できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="e13fe-137">`DisplayFormat` 属性は単独で使用できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="e13fe-138">一般的には、`DataType` 属性を `DisplayFormat` 属性と一緒に使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="e13fe-139">`DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="e13fe-140">`DataType` 属性には、`DisplayFormat` では得られない以下のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="e13fe-141">ブラウザーで HTML5 機能を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="e13fe-142">たとえば、カレンダー コントロール、ロケールに適した通貨記号、メール リンク、クライアント側の入力検証を表示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="e13fe-143">既定では、ブラウザーで、ロケールに基づいて正しい書式を使用してデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="e13fe-144">詳細については、[\<入力> タグ ヘルパーに関するドキュメント](xref:mvc/views/working-with-forms#the-input-tag-helper)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="e13fe-145">StringLength 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="e13fe-146">データ検証規則と検証エラー メッセージは、属性を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="e13fe-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 属性では、データ フィールドで使用できる最小文字長と最大文字長を指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="e13fe-148">示されているコードでは、名前で使用可能な文字数が 50 字以下に制限されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="e13fe-149">文字列の最小長を設定する例は、[後で](#the-required-attribute)示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="e13fe-150">また、`StringLength` 属性ではクライアント側とサーバー側の検証も提供されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="e13fe-151">最小値は、データベース スキーマに影響しません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="e13fe-152">`StringLength` 属性では、ユーザーが名前に空白を入力しないようにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="e13fe-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 属性は、入力に制限を適用するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="e13fe-154">たとえば、次のコードでは、最初の文字を大文字にし、残りの文字をアルファベット順にすることを要求します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e13fe-156">**SQL Server オブジェクト エクスプローラー** (SSOX) で、**Student** テーブルをダブルクリックして、Student テーブル デザイナーを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移行前の SSOX の Students テーブル](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="e13fe-158">上の図には `Student` テーブルのスキーマが表示されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="e13fe-159">名前フィールドは `nvarchar(MAX)` 型です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="e13fe-160">このチュートリアルの後半で移行を作成して適用すると、文字列長属性の結果として名前フィールドは `nvarchar(50)` になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e13fe-162">SQLite ツールで、`Student` テーブルの列の定義を調べます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="e13fe-163">名前フィールドは `Text` 型です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-163">The name fields have type `Text`.</span></span> <span data-ttu-id="e13fe-164">最初の名前フィールドの名前は `FirstMidName` であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="e13fe-165">次のセクションでは、その列の名前を `FirstName` に変更します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="e13fe-166">Column 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="e13fe-167">属性で、データベースへのクラスとプロパティのマッピング方法を制御することができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="e13fe-168">`Student` モデルでは、`Column` 属性を使用して、`FirstMidName` プロパティの名前をデータベースの "FirstName" にマッピングします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="e13fe-169">データベースが作成されたときに、列名でモデルのプロパティ名が使用されます (`Column` 属性が使用されている場合を除く)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="e13fe-170">`Student` モデルでは名フィールドに対して `FirstMidName` が使用されます。これは、フィールドにミドル ネームも含まれている場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="e13fe-171">`[Column]` 属性により、データ モデル内の `Student.FirstMidName` が、`Student` テーブルの `FirstName` 列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="e13fe-172">`Column` 属性を追加すると、`SchoolContext` をサポートするモデルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="e13fe-173">`SchoolContext` をサポートするモデルはデータベースと一致しなくなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="e13fe-174">そのような不一致は、このチュートリアルの後半で移行を追加することによって解決されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="e13fe-175">Required 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="e13fe-176">`Required` 属性では、名前プロパティの必須フィールドを作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="e13fe-177">値の型 (例: `DateTime`、`int`、`double`) などの null 非許容型では、`Required` 属性は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="e13fe-178">null にできない型は自動的に必須フィールドとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="e13fe-179">`Required` 属性は、`StringLength` 属性の最小長パラメーターに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-179">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="e13fe-180">Display 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-180">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="e13fe-181">`Display` 属性では、テキスト ボックスのキャプションを "First Name"、"Last Name"、"Full Name"、"Enrollment Date" にするよう指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-181">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="e13fe-182">既定のキャプションには、"Lastname" のように、単語を区切るスペースはありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-182">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="e13fe-183">移行を作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-183">Create a migration</span></span>

<span data-ttu-id="e13fe-184">アプリを実行して [Students] ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-184">Run the app and go to the Students page.</span></span> <span data-ttu-id="e13fe-185">例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-185">An exception is thrown.</span></span> <span data-ttu-id="e13fe-186">`[Column]` 属性があるため、EF では `FirstName` という名前の列が検索されることが予想されますが、データベースの列名はまだ `FirstMidName` です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-186">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-187">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e13fe-188">エラー メッセージは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-188">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="e13fe-189">PMC で、以下のコマンドを入力し、新しい移行を作成してデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-189">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="e13fe-190">これらの最初のコマンドでは、以下の警告メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-190">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="e13fe-191">名前フィールドは現在、50 文字に制限されているため、警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-191">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="e13fe-192">データベースの名前が 50 文字を超えた場合、51 番目から最後までの文字が失われます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-192">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="e13fe-193">SSOX で Student テーブルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-193">Open the Student table in SSOX:</span></span>

  ![移行後の SSOX の Students テーブル](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="e13fe-195">移行が適用される前の名前列の型は [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) でした。</span><span class="sxs-lookup"><span data-stu-id="e13fe-195">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="e13fe-196">現在の名前列は `nvarchar(50)` です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-196">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="e13fe-197">列名は `FirstMidName` から `FirstName` に変わりました。</span><span class="sxs-lookup"><span data-stu-id="e13fe-197">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e13fe-199">エラー メッセージは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-199">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="e13fe-200">プロジェクト フォルダーでコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-200">Open a command window in the project folder.</span></span> <span data-ttu-id="e13fe-201">以下のコマンドを入力し、新しい移行を作成してデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-201">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="e13fe-202">データベースの更新コマンドを実行すると、次の例のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-202">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="e13fe-203">このチュートリアルでは、このエラーを回避する方法は、最初の移行を削除してから再作成することです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-203">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="e13fe-204">詳しくは、[移行チュートリアル](xref:data/ef-rp/migrations)の先頭にある SQLite の警告に関する注意をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-204">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="e13fe-205">*Migrations* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-205">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="e13fe-206">次のコマンドを実行して、データベースを削除し、新しい初期移行を作成して、移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-206">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="e13fe-207">SQLite ツールで Student テーブルを確認します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-207">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="e13fe-208">FirstMidName だった列は FirstName になっています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-208">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="e13fe-209">アプリを実行して [Students] ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-209">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="e13fe-210">日付と共に時刻が入力および表示されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-210">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="e13fe-211">**[新規作成]** を選択し、50 文字を超える名前を入力してみます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-211">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="e13fe-212">次のセクションでは、いくつかのステージでアプリをビルドします。その場合、コンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-212">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="e13fe-213">手順では、アプリをビルドするタイミングを指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-213">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="e13fe-214">Instructor エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-214">The Instructor Entity</span></span>

![Instructor エンティティ](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="e13fe-216">以下のコードを使用して、*Models/Instructor.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-216">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="e13fe-217">複数の属性を 1 行に配置することができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-217">Multiple attributes can be on one line.</span></span> <span data-ttu-id="e13fe-218">`HireDate` 属性は次のように記述できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-218">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="e13fe-219">ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-219">Navigation properties</span></span>

<span data-ttu-id="e13fe-220">`CourseAssignments` と `OfficeAssignment` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-220">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="e13fe-221">講師は任意の数のコースを担当できるため、`CourseAssignments` はコレクションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-221">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="e13fe-222">講師は最大で 1 つのオフィスを持つことができるので、`OfficeAssignment` プロパティには 1 つの `OfficeAssignment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-222">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="e13fe-223">オフィスが割り当てられていない場合、`OfficeAssignment` は null です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-223">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="e13fe-224">OfficeAssignment エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-224">The OfficeAssignment entity</span></span>

![OfficeAssignment エンティティ](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="e13fe-226">以下のコードを使用して、*Models/OfficeAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-226">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="e13fe-227">Key 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-227">The Key attribute</span></span>

<span data-ttu-id="e13fe-228">`[Key]` 属性は、プロパティ名が classnameID や ID 以外である場合に、主キー (PK) としてプロパティを識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-228">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="e13fe-229">`Instructor` エンティティと `OfficeAssignment` エンティティの間には一対ゼロまたは一対一のリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-229">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="e13fe-230">オフィスが割り当てられている講師についてのみ、オフィス割り当てが存在します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-230">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="e13fe-231">`OfficeAssignment` PK は、`Instructor` エンティティに対する外部キー (FK) でもあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-231">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="e13fe-232">`InstructorID` が ID または classnameID の名前付け規則に従っていないため、EF Core では、`InstructorID` が `OfficeAssignment` の PK として自動的に認識されません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-232">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="e13fe-233">したがって、`Key` 属性は PK として `InstructorID` を識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="e13fe-234">列は依存リレーションシップに対するものであるため、既定では EF Core はキーを非データベース生成として扱います。</span><span class="sxs-lookup"><span data-stu-id="e13fe-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="e13fe-235">Instructor ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-235">The Instructor navigation property</span></span>

<span data-ttu-id="e13fe-236">特定の講師に対して `OfficeAssignment` 行が存在しない可能性があるため、`Instructor.OfficeAssignment` ナビゲーション プロパティは null でもかまいません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-236">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="e13fe-237">講師にオフィスが割り当てられていない可能性がある。</span><span class="sxs-lookup"><span data-stu-id="e13fe-237">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="e13fe-238">外部キー `InstructorID` の型は `int` であり、null 非許容値型であるため、`OfficeAssignment.Instructor` ナビゲーション プロパティには常に講師のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-238">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="e13fe-239">オフィス割り当ては講師なしでは存在できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-239">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="e13fe-240">`Instructor` エンティティに関連する `OfficeAssignment` エンティティがある場合、各エンティティにはそのナビゲーション プロパティの別のエンティティへの参照があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-240">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="e13fe-241">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-241">The Course Entity</span></span>

![Course エンティティ](complex-data-model/_static/course-entity.png)

<span data-ttu-id="e13fe-243">以下のコードを使用して、*Models/Course.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-243">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="e13fe-244">`Course` エンティティには外部キー (FK) プロパティ `DepartmentID` があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-244">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="e13fe-245">`DepartmentID` は関連する `Department` エンティティを指します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-245">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="e13fe-246">`Course` エンティティには `Department` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-246">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="e13fe-247">EF Core では、モデルに関連エンティティのナビゲーション プロパティがある場合、データ モデルの外部キー プロパティは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-247">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="e13fe-248">EF Core は、必要に応じて、データベースで自動的に FK を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-248">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="e13fe-249">EF Core は、自動的に作成された FK に対して、[シャドウ プロパティ](/ef/core/modeling/shadow-properties)を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-249">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="e13fe-250">ただし、データ モデルに FK を明示的に含めると、更新をより簡単かつ効率的に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-250">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="e13fe-251">たとえば、FK プロパティ `DepartmentID` が含まれて*いない* モデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-251">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="e13fe-252">Course エンティティが編集用にフェッチされた場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-252">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="e13fe-253">明示的に読み込まれない場合、`Department` プロパティは null になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-253">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="e13fe-254">Course エンティティを更新するには、`Department` エンティティを最初にフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-254">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="e13fe-255">FK エンティティ `DepartmentID` がデータ モデルに含まれている場合は、更新前に `Department` エンティティをフェッチする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-255">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="e13fe-256">DatabaseGenerated 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-256">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="e13fe-257">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 属性では、PK をデータベースで生成するのではなく、アプリケーションで提供するように指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-257">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="e13fe-258">既定では、EF Core では PK 値がデータベースによって生成されるものと想定されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-258">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="e13fe-259">通常は、データベースで生成するのが最善です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-259">Database-generated is generally the best approach.</span></span> <span data-ttu-id="e13fe-260">`Course` エンティティの場合、PK はユーザーが指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-260">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="e13fe-261">たとえば、数学科の場合は 1000 シリーズ、英文科の場合は 2000 シリーズなどのコース番号となります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-261">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="e13fe-262">`DatabaseGenerated` 属性は、既定値を生成する場合にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-262">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="e13fe-263">たとえば、データベースでは、行が作成または更新された日付を記録するための日付フィールドを自動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-263">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="e13fe-264">詳細については、「[生成される値](/ef/core/modeling/generated-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-264">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e13fe-265">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-265">Foreign key and navigation properties</span></span>

<span data-ttu-id="e13fe-266">`Course` エンティティの外部キー (FK) プロパティとナビゲーション プロパティには、以下のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-266">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="e13fe-267">コースが 1 つの学科に割り当てられています。したがって、`DepartmentID` FK と `Department` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-267">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="e13fe-268">コースには任意の数の学生が登録できるため、`Enrollments` ナビゲーション プロパティはコレクションとなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-268">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="e13fe-269">コースは複数の講師が担当する場合があるため、`CourseAssignments` ナビゲーション プロパティはコレクションとなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-269">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="e13fe-270">`CourseAssignment` については、[後で](#many-to-many-relationships)説明します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-270">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="e13fe-271">Department エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-271">The Department entity</span></span>

![Department エンティティ](complex-data-model/_static/department-entity.png)

<span data-ttu-id="e13fe-273">以下のコードを使用して、*Models/Department.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-273">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="e13fe-274">Column 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-274">The Column attribute</span></span>

<span data-ttu-id="e13fe-275">これまでは、`Column` 属性が列名のマッピングを変更するために使用されました。</span><span class="sxs-lookup"><span data-stu-id="e13fe-275">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="e13fe-276">`Department` エンティティのコードでは、`Column` 属性は SQL データ型のマッピングを変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-276">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="e13fe-277">`Budget` 列は、データベースでは次のように SQL Server の money 型を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-277">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="e13fe-278">通常、列マッピングは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-278">Column mapping is generally not required.</span></span> <span data-ttu-id="e13fe-279">EF Core では、プロパティの CLR 型に基づいて、適切な SQL Server のデータ型が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-279">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="e13fe-280">CLR `decimal` 型は SQL Server の `decimal` 型にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-280">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="e13fe-281">`Budget` は通貨用であり、通貨には money データ型がより適しています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-281">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e13fe-282">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-282">Foreign key and navigation properties</span></span>

<span data-ttu-id="e13fe-283">FK およびナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-283">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="e13fe-284">学科には管理者が存在する場合とそうでない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-284">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="e13fe-285">管理者は常に講師です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-285">An administrator is always an instructor.</span></span> <span data-ttu-id="e13fe-286">したがって、`InstructorID` プロパティは `Instructor` エンティティに対する FK として含まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-286">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="e13fe-287">ナビゲーション プロパティは `Administrator` という名前ですが、`Instructor` エンティティを保持します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-287">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="e13fe-288">上のコードの疑問符 (?) は、プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-288">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="e13fe-289">学科には複数のコースがある場合があるため、Courses ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-289">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="e13fe-290">規則により、EF Core では null 非許容の FK と多対多リレーションシップに対して連鎖削除が有効になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-290">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="e13fe-291">この既定の動作では、循環連鎖削除規則が適用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-291">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="e13fe-292">循環連鎖削除規則が適用されると、移行の追加時に例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-292">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="e13fe-293">たとえば、`Department.InstructorID` プロパティが null 非許容として定義されている場合、EF Core では連鎖削除規則が構成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-293">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="e13fe-294">この場合、管理者として割り当てられた講師が削除されると、部門は削除されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-294">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="e13fe-295">このシナリオでは、制限規則がより合理的になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-295">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="e13fe-296">次の fluent API では、制限規則が設定されて、連鎖削除が無効になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-296">The following fluent API would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="e13fe-297">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-297">The Enrollment entity</span></span>

<span data-ttu-id="e13fe-298">1 件の登録レコードは、1 人の学生が受講する 1 つのコースに対するものです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-298">An enrollment record is for one course taken by one student.</span></span>

![Enrollment エンティティ](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="e13fe-300">以下のコードを使用して、*Models/Enrollment.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-300">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e13fe-301">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-301">Foreign key and navigation properties</span></span>

<span data-ttu-id="e13fe-302">FK プロパティとナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-302">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="e13fe-303">登録レコードは 1 つのコースに対するものであるため、`CourseID` FK プロパティと `Course` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-303">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="e13fe-304">登録レコードは 1 人の学生に対するものであるため、`StudentID` FK プロパティと `Student` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-304">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="e13fe-305">多対多リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="e13fe-305">Many-to-Many Relationships</span></span>

<span data-ttu-id="e13fe-306">`Student` エンティティと `Course` エンティティの間には多対多リレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-306">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="e13fe-307">`Enrollment` エンティティは、データベースで*ペイロードがある*多対多結合テーブルとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-307">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="e13fe-308">"ペイロードがある" とは、`Enrollment` テーブルに、結合テーブルの FK 以外に追加データが含まれていることを意味します (ここでは PK と `Grade`)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-308">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="e13fe-309">次の図は、エンティティ図でこれらのリレーションシップがどのようになるかを示しています</span><span class="sxs-lookup"><span data-stu-id="e13fe-309">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="e13fe-310">(この図は、EF 6.x 用の [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) を使用して生成されたものです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-310">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="e13fe-311">このチュートリアルでは図は作成しません)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-311">Creating the diagram isn't part of the tutorial.)</span></span>

![Student と Course の多対多リレーションシップ](complex-data-model/_static/student-course.png)

<span data-ttu-id="e13fe-313">各リレーションシップ線の一方の端に 1 が、もう一方の端にアスタリスク (\*) があり、1 対多リレーションシップであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-313">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="e13fe-314">`Enrollment` テーブルに成績情報が含まれていない場合、含める必要があるのは 2 つの FK (`CourseID` と `StudentID`) のみです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-314">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="e13fe-315">ペイロードがない多対多結合テーブルは純粋結合テーブル (PJT) と呼ばれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-315">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="e13fe-316">`Instructor` および `Course` エンティティには、純粋結合テーブルを使用する多対多リレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-316">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="e13fe-317">メモ:EF 6.x では多対多リレーションシップの暗黙の結合テーブルがサポートされますが、EF Core ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-317">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="e13fe-318">詳細については、「[Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)」 (EF Core 2.0 の多対多リレーションシップ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-318">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="e13fe-319">CourseAssignment エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-319">The CourseAssignment entity</span></span>

![CourseAssignment エンティティ](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="e13fe-321">以下のコードを使用して、*Models/CourseAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-321">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="e13fe-322">講師とコースの多対多リレーションシップには結合テーブルが必要であり、その結合テーブルのエンティティは CourseAssignment です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-322">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="e13fe-324">結合エンティティには `EntityName1EntityName2` という名前が付けるのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-324">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="e13fe-325">たとえば、このパターンを使用する講師対コースの結合テーブルは `CourseInstructor` になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-325">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="e13fe-326">ただし、リレーションシップを説明する名前を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-326">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="e13fe-327">データ モデルは始めは単純なものであっても大きくなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-327">Data models start out simple and grow.</span></span> <span data-ttu-id="e13fe-328">ペイロードがない結合テーブル (PJT) は、頻繁に更新されてペイロードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-328">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="e13fe-329">最初にわかりやすいエンティティ名を付けておけば、結合テーブルが変更されたときに名前を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-329">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="e13fe-330">結合エンティティでは、ビジネス ドメインに独自の自然な (場合によっては 1 単語の) 名前を指定することが理想的です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-330">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="e13fe-331">たとえば、Books と Customers は Ratings という結合エンティティでリンクできます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-331">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="e13fe-332">講師対コースの多対多リレーションシップの場合、`CourseAssignment` は `CourseInstructor` より優先されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-332">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="e13fe-333">複合キー</span><span class="sxs-lookup"><span data-stu-id="e13fe-333">Composite key</span></span>

<span data-ttu-id="e13fe-334">`CourseAssignment` の 2 つの FK (`InstructorID` と `CourseID`) を組み合わせて使用し、`CourseAssignment` テーブルの各行を一意に識別します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-334">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="e13fe-335">`CourseAssignment` には専用の PK は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-335">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="e13fe-336">`InstructorID` および `CourseID` プロパティは複合 PK として機能します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-336">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="e13fe-337">EF Core に複合 PK を指定する唯一の方法は、*fluent API* を使用することです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-337">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="e13fe-338">次のセクションでは、複合 PK の構成方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-338">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="e13fe-339">複合キーにより、次のことが保証されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-339">The composite key ensures that:</span></span>

* <span data-ttu-id="e13fe-340">1 つのコースに対して複数の行が許可される。</span><span class="sxs-lookup"><span data-stu-id="e13fe-340">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="e13fe-341">1 人の講師に対して複数の行が許可される。</span><span class="sxs-lookup"><span data-stu-id="e13fe-341">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="e13fe-342">同じ講師とコースに対して複数の行が許可されない。</span><span class="sxs-lookup"><span data-stu-id="e13fe-342">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="e13fe-343">`Enrollment` 結合エンティティでは独自の PK を定義するため、このような重複が考えられます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-343">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="e13fe-344">このような重複を防ぐには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-344">To prevent such duplicates:</span></span>

* <span data-ttu-id="e13fe-345">FK フィールドに一意のインデックスを追加する。または</span><span class="sxs-lookup"><span data-stu-id="e13fe-345">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="e13fe-346">`CourseAssignment` と同様の複合主キーを使用して、`Enrollment` を構成する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-346">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="e13fe-347">詳細については、「[インデックス](/ef/core/modeling/indexes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-347">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="e13fe-348">データベース コンテキストを更新する</span><span class="sxs-lookup"><span data-stu-id="e13fe-348">Update the database context</span></span>

<span data-ttu-id="e13fe-349">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-349">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="e13fe-350">上のコードでは新しいエンティティが追加され、`CourseAssignment` エンティティの複合 PK が構成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-350">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="e13fe-351">属性の代わりに fluent API を使用する</span><span class="sxs-lookup"><span data-stu-id="e13fe-351">Fluent API alternative to attributes</span></span>

<span data-ttu-id="e13fe-352">上のコードの `OnModelCreating` メソッドでは、*fluent API* を使用して EF Core の動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-352">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="e13fe-353">API は "fluent" と呼ばれます。これは、多くの場合、一連のメソッド呼び出しを単一のステートメントにまとめて使用されるためです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-353">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="e13fe-354">[次のコード](/ef/core/modeling/#use-fluent-api-to-configure-a-model)は fluent API の例です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-354">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="e13fe-355">このチュートリアルでは、属性で実行できないデータベース マッピングの場合にのみ、fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-355">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="e13fe-356">ただし、fluent API では、属性で実行できる書式設定、検証、マッピング規則のほとんどを指定できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-356">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="e13fe-357">`MinimumLength` などの一部の属性は fluent API で適用できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-357">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="e13fe-358">`MinimumLength` ではスキーマを変更せず、最小長の検証規則のみを適用します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-358">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="e13fe-359">一部の開発者は fluent API のみを使用することを選ぶため、エンティティ クラスを "クリーン" な状態に保つことができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-359">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="e13fe-360">属性と fluent API を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-360">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="e13fe-361">(複合 PK を指定して) fluent API でのみ実行できる構成がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-361">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="e13fe-362">属性 (`MinimumLength`) でのみ実行できる構成もいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-362">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="e13fe-363">次のように、fluent API または属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-363">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="e13fe-364">これら 2 つの方法のいずれかを選択する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-364">Choose one of these two approaches.</span></span>
* <span data-ttu-id="e13fe-365">できるだけ一貫性を保つために選択した方法を使用する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-365">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="e13fe-366">このチュートリアルで使用する属性のいくつかは、次の用途に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-366">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="e13fe-367">検証のみ (`MinimumLength` など)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-367">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="e13fe-368">EF Core 構成のみ (`HasKey` など)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-368">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="e13fe-369">検証と EF Core の構成 (`[StringLength(50)]` など)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-369">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="e13fe-370">属性と fluent API の詳細については、「[構成の方法](/ef/core/modeling/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-370">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="e13fe-371">エンティティ図</span><span class="sxs-lookup"><span data-stu-id="e13fe-371">Entity diagram</span></span>

<span data-ttu-id="e13fe-372">次の図では、完成した School モデルに対して EF Power Tools で作成される図を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-372">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

<span data-ttu-id="e13fe-374">上の図には以下が示されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-374">The preceding diagram shows:</span></span>

* <span data-ttu-id="e13fe-375">いくつかの一対多リレーションシップの線 (1 対 \*)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-375">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="e13fe-376">`Instructor` エンティティと `OfficeAssignment` エンティティの間の一対ゼロまたは一対一リレーションシップの線 (1 対 0..1)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-376">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="e13fe-377">`Instructor` エンティティと `Department` エンティティの間のゼロ対一またはゼロ対多リレーションシップの線 (1 対 0..1)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-377">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="e13fe-378">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="e13fe-378">Seed the database</span></span>

<span data-ttu-id="e13fe-379">*Data/DbInitializer.cs* のコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-379">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="e13fe-380">上のコードでは、新しいエンティティのシード データが提供されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-380">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="e13fe-381">このコードのほとんどで新しいエンティティ オブジェクトが作成され、サンプル データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-381">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="e13fe-382">サンプル データはテストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-382">The sample data is used for testing.</span></span> <span data-ttu-id="e13fe-383">多対多結合テーブルがシードされる方法の例については、`Enrollments` と `CourseAssignments` を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-383">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="e13fe-384">移行を追加する</span><span class="sxs-lookup"><span data-stu-id="e13fe-384">Add a migration</span></span>

<span data-ttu-id="e13fe-385">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-385">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-386">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e13fe-387">PMC で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-387">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="e13fe-388">上のコマンドは、考えられるデータ損失に関する警告を表示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-388">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="e13fe-389">`database update` コマンドを実行すると、次のエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-389">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="e13fe-390">次のセクションでは、このエラーの対処方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-390">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e13fe-392">移行を追加して `database update` コマンドを実行すると、次のエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-392">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="e13fe-393">次のセクションでは、このエラーの回避方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-393">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="e13fe-394">移行を適用するか、削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-394">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="e13fe-395">既存のデータベースができたので、変更を適用する方法について検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-395">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="e13fe-396">このチュートリアルでは、2 つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-396">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="e13fe-397">[データベースを削除して再作成する](#drop)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-397">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="e13fe-398">SQLite を使用している場合は、このセクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-398">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="e13fe-399">[移行を既存のデータベースに適用する](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-399">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="e13fe-400">このセクションの手順は SQL Server にのみ使用でき、**SQLite では使用できません**。</span><span class="sxs-lookup"><span data-stu-id="e13fe-400">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="e13fe-401">どちらの選択肢も SQL Server で機能します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-401">Either choice works for SQL Server.</span></span> <span data-ttu-id="e13fe-402">移行適用方法はより複雑で時間がかかりますが、実際の運用環境では推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-402">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="e13fe-403">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-403">Drop and re-create the database</span></span>

<span data-ttu-id="e13fe-404">SQL Server を使用していて、次のセクションの移行適用方法を実行する場合は、[このセクションをスキップします](#apply-the-migration)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-404">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="e13fe-405">EF Core に新しいデータベースを強制的に作成させるには、データベースを削除して更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-405">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e13fe-407">**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-407">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="e13fe-408">*Migrations* フォルダーを削除し、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-408">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e13fe-410">コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-410">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="e13fe-411">プロジェクト フォルダーには、*ContosoUniversity.csproj* ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-411">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="e13fe-412">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-412">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="e13fe-413">*Migrations* フォルダーを削除し、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-413">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="e13fe-414">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-414">Run the app.</span></span> <span data-ttu-id="e13fe-415">アプリを実行すると `DbInitializer.Initialize` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-415">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="e13fe-416">`DbInitializer.Initialize` では、新しいデータベースが設定されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-416">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-417">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-417">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e13fe-418">SSOX でデータベースを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-418">Open the database in SSOX:</span></span>

* <span data-ttu-id="e13fe-419">SSOX が既に開いている場合は、 **[更新]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-419">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="e13fe-420">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-420">Expand the **Tables** node.</span></span> <span data-ttu-id="e13fe-421">作成されたテーブルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-421">The created tables are displayed.</span></span>

  ![SSOX のテーブル](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="e13fe-423">**CourseAssignment** テーブルを確認します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-423">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="e13fe-424">**CourseAssignment** テーブルを右クリックして、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-424">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="e13fe-425">**CourseAssignment** テーブルにデータが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-425">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX の CourseAssignment データ](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e13fe-428">SQLite ツールを使用してデータベースを確認します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-428">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="e13fe-429">新しいテーブルと列。</span><span class="sxs-lookup"><span data-stu-id="e13fe-429">New tables and columns.</span></span>
* <span data-ttu-id="e13fe-430">テーブル内のシードされたデータ (**CourseAssignment**テーブルなど)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-430">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="e13fe-431">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="e13fe-431">Apply the migration</span></span>

<span data-ttu-id="e13fe-432">このセクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-432">This section is optional.</span></span> <span data-ttu-id="e13fe-433">以下の手順は、SQL Server LocalDB に対してだけ、前の「[データベースを削除して再作成する](#drop)」セクションをスキップした場合にのみ、使用できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-433">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="e13fe-434">既存のデータで移行が実行されている場合、既存のデータでは満たされない FK 制約が存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="e13fe-435">運用データを使用する場合は、既存のデータを移行するための手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="e13fe-436">このセクションでは、FK 制約違反の修正例を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="e13fe-437">これらのコードをバックアップせずに変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="e13fe-438">前の「[データベースを削除して再作成する](#drop)」セクションを完了している場合は、これらのコードを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-438">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="e13fe-439">*{timestamp}_ComplexDataModel.cs* ファイルには次のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="e13fe-440">上のコードでは、null 非許容の `DepartmentID` FK が `Course` テーブルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="e13fe-441">前のチュートリアルのデータベースには `Course` の行が含まれるため、テーブルを移行して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-441">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="e13fe-442">既存のデータを使用して `ComplexDataModel` の移行を実行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="e13fe-443">コードを変更して、新しい列 (`DepartmentID`) に既定値を設定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="e13fe-444">"Temp" という名前の偽の学科を作成し、既定の学科として機能するようにします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="e13fe-445">外部キー制約を修正する</span><span class="sxs-lookup"><span data-stu-id="e13fe-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="e13fe-446">`ComplexDataModel` 移行クラスで、`Up` メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-446">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="e13fe-447">*{timestamp}_ComplexDataModel.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="e13fe-448">`DepartmentID` 列を `Course` テーブルに追加するコードの行をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="e13fe-449">次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-449">Add the following highlighted code.</span></span> <span data-ttu-id="e13fe-450">新しいコードが `.CreateTable( name: "Department"` ブロックの後に配置されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="e13fe-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span><span class="sxs-lookup"><span data-stu-id="e13fe-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="e13fe-452">前述の変更に伴い、既存の `Course` 行が、`ComplexDataModel.Up` メソッドの実行後に "Temp" 学科に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-452">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="e13fe-453">ここで示す状況の処理方法は、このチュートリアルのために簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-453">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="e13fe-454">運用アプリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="e13fe-454">A production app would:</span></span>

* <span data-ttu-id="e13fe-455">コードまたはスクリプトを組み込み、`Department` 行と関連する `Course` 行を新しい `Department` 行に追加します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-455">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="e13fe-456">`Course.DepartmentID` の既定値や "Temp" 学科は使用しません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-456">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e13fe-458">**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-458">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="e13fe-459">`DbInitializer.Initialize` メソッドは空のデータベースでのみ動作するように設計されているので、Student テーブルと Course テーブルのすべての行を削除するには SSOX を使用します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-459">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="e13fe-460">(連鎖削除によって Enrollment テーブルが処理されます。)</span><span class="sxs-lookup"><span data-stu-id="e13fe-460">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-461">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-461">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e13fe-462">Visual Studio Code で SQL Server LocalDB を使用している場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-462">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="e13fe-463">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-463">Run the app.</span></span> <span data-ttu-id="e13fe-464">アプリを実行すると `DbInitializer.Initialize` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-464">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="e13fe-465">`DbInitializer.Initialize` では、新しいデータベースが設定されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-465">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e13fe-466">次の手順</span><span class="sxs-lookup"><span data-stu-id="e13fe-466">Next steps</span></span>

<span data-ttu-id="e13fe-467">次の 2 つのチュートリアルでは、関連データを読み取って更新する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-467">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e13fe-468">[前のチュートリアル](xref:data/ef-rp/migrations)
> [次のチュートリアル](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="e13fe-468">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e13fe-469">前のチュートリアルでは、3 つのエンティティで構成された基本的なデータ モデルを使用して作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="e13fe-469">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="e13fe-470">このチュートリアルでは、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="e13fe-470">In this tutorial:</span></span>

* <span data-ttu-id="e13fe-471">エンティティとリレーションシップをさらに追加する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-471">More entities and relationships are added.</span></span>
* <span data-ttu-id="e13fe-472">書式設定、検証、データベース マッピングの規則を指定して、データ モデルをカスタマイズする。</span><span class="sxs-lookup"><span data-stu-id="e13fe-472">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="e13fe-473">完成したデータ モデルのエンティティ クラスは、次の図のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-473">The entity classes for the completed data model are shown in the following illustration:</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

<span data-ttu-id="e13fe-475">解決できない問題が発生した場合は、[完成したアプリ](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)をダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-475">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="e13fe-476">属性を使用してデータ モデルをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="e13fe-476">Customize the data model with attributes</span></span>

<span data-ttu-id="e13fe-477">このセクションでは、属性を使用してデータ モデルをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-477">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="e13fe-478">DataType 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-478">The DataType attribute</span></span>

<span data-ttu-id="e13fe-479">学生のページには現在、登録日の時刻が表示されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-479">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="e13fe-480">通常、日付フィールドには日付のみが表示され、時刻は表示されません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-480">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="e13fe-481">以下の強調表示されているコードを使用して、*Models/Student.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-481">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="e13fe-482">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 属性では、データベースの組み込み型よりも具体的なデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-482">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="e13fe-483">ここでは、日付と時刻ではなく、日付のみを表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-483">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="e13fe-484">[DataType 列挙型](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くのデータ型のために用意されています。また、`DataType` 属性を使用して、アプリで型固有の機能を自動的に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-484">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="e13fe-485">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-485">For example:</span></span>

* <span data-ttu-id="e13fe-486">`mailto:` リンクは `DataType.EmailAddress` に対して自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-486">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="e13fe-487">ほとんどのブラウザーでは、`DataType.Date` に日付セレクターが提供されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-487">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="e13fe-488">`DataType` 属性は、HTML 5 ブラウザーが使用する HTML 5 `data-` (データ ダッシュと読む) 属性を出力します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-488">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="e13fe-489">`DataType` 属性では検証は提供されません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-489">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="e13fe-490">`DataType.Date` は、表示される日付の書式を指定しません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-490">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="e13fe-491">既定で、日付フィールドはサーバーの [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) に基づき、既定の書式に従って表示されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-491">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="e13fe-492">`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-492">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="e13fe-493">`ApplyFormatInEditMode` 設定では、書式設定を編集 UI にも適用する必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-493">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="e13fe-494">一部のフィールドでは `ApplyFormatInEditMode` を使用できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-494">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="e13fe-495">たとえば、通貨記号は一般的に編集テキスト ボックスには表示できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-495">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="e13fe-496">`DisplayFormat` 属性は単独で使用できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-496">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="e13fe-497">一般的には、`DataType` 属性を `DisplayFormat` 属性と一緒に使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-497">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="e13fe-498">`DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-498">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="e13fe-499">`DataType` 属性には、`DisplayFormat` では得られない以下のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-499">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="e13fe-500">ブラウザーで HTML5 機能を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-500">The browser can enable HTML5 features.</span></span> <span data-ttu-id="e13fe-501">たとえば、カレンダー コントロール、ロケールに適した通貨記号、メール リンク、クライアント側の入力検証などを表示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-501">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="e13fe-502">既定では、ブラウザーで、ロケールに基づいて正しい書式を使用してデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-502">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="e13fe-503">詳細については、[\<入力> タグ ヘルパーに関するドキュメント](xref:mvc/views/working-with-forms#the-input-tag-helper)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-503">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="e13fe-504">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-504">Run the app.</span></span> <span data-ttu-id="e13fe-505">Students インデックス ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-505">Navigate to the Students Index page.</span></span> <span data-ttu-id="e13fe-506">時刻は表示されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e13fe-506">Times are no longer displayed.</span></span> <span data-ttu-id="e13fe-507">`Student` モデルを使用するすべてのビューに、時刻なしの日付が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-507">Every view that uses the `Student` model displays the date without time.</span></span>

![時刻なしの日付が表示されている Students インデックス ページ](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="e13fe-509">StringLength 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-509">The StringLength attribute</span></span>

<span data-ttu-id="e13fe-510">データ検証規則と検証エラー メッセージは、属性を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-510">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="e13fe-511">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 属性では、データ フィールドで使用できる最小文字長と最大文字長を指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-511">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="e13fe-512">また、`StringLength` 属性ではクライアント側とサーバー側の検証も提供されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-512">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="e13fe-513">最小値は、データベース スキーマに影響しません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-513">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="e13fe-514">`Student` モデルを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-514">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="e13fe-515">上のコードでは、名前で使用可能な文字数を 50 に制限します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-515">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="e13fe-516">`StringLength` 属性では、ユーザーが名前に空白を入力しないようにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-516">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="e13fe-517">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 属性は、入力に制限を適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-517">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="e13fe-518">たとえば、次のコードでは、最初の文字を大文字にし、残りの文字をアルファベット順にすることを要求します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-518">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="e13fe-519">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-519">Run the app:</span></span>

* <span data-ttu-id="e13fe-520">Students のページに移動します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-520">Navigate to the Students page.</span></span>
* <span data-ttu-id="e13fe-521">**[新規作成]** を選択し、50 文字を超える名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-521">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="e13fe-522">**[作成]** を選択すると、クライアント側の検証でエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-522">Select **Create**, client-side validation shows an error message.</span></span>

![文字列長エラーが表示されている Students インデックス ページ](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="e13fe-524">**SQL Server オブジェクト エクスプローラー** (SSOX) で、**Student** テーブルをダブルクリックして、Student テーブル デザイナーを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-524">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移行前の SSOX の Students テーブル](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="e13fe-526">上の図には `Student` テーブルのスキーマが表示されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-526">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="e13fe-527">DB では移行が実行されていないため、名前フィールドに `nvarchar(MAX)` 型があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-527">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="e13fe-528">このチュートリアルの後半で移行が実行されたときに、名前フィールドが `nvarchar(50)` になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-528">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="e13fe-529">Column 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-529">The Column attribute</span></span>

<span data-ttu-id="e13fe-530">属性で、データベースへのクラスとプロパティのマッピング方法を制御することができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-530">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="e13fe-531">このセクションでは、`Column` 属性を使用して、`FirstMidName` プロパティの名前を DB の "FirstName" にマッピングします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-531">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="e13fe-532">DB が作成されたときに、列名でモデルのプロパティ名が使用されます (`Column` 属性が使用されている場合を除く)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-532">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="e13fe-533">`Student` モデルでは名フィールドに対して `FirstMidName` が使用されます。これは、フィールドにミドル ネームも含まれている場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-533">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="e13fe-534">以下の強調表示されているコードを使用して、*Student.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-534">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="e13fe-535">前述の変更に伴い、アプリの `Student.FirstMidName` は `Student` テーブルの `FirstName` 列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-535">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="e13fe-536">`Column` 属性を追加すると、`SchoolContext` をサポートするモデルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-536">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="e13fe-537">`SchoolContext` をサポートするモデルはデータベースと一致しなくなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-537">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="e13fe-538">移行を適用する前にアプリを実行した場合は、次の例外が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-538">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="e13fe-539">DB を更新するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-539">To update the DB:</span></span>

* <span data-ttu-id="e13fe-540">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-540">Build the project.</span></span>
* <span data-ttu-id="e13fe-541">プロジェクト フォルダーでコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-541">Open a command window in the project folder.</span></span> <span data-ttu-id="e13fe-542">以下のコマンドを入力し、新しい移行を作成して DB を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-542">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-543">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="e13fe-545">`migrations add ColumnFirstName` コマンドでは、以下の警告メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-545">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="e13fe-546">名前フィールドは現在、50 文字に制限されているため、警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-546">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="e13fe-547">DB の名前が 50 文字を超えた場合、51 番目から最後までの文字が失われます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-547">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="e13fe-548">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-548">Test the app.</span></span>

<span data-ttu-id="e13fe-549">SSOX で Student テーブルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-549">Open the Student table in SSOX:</span></span>

![移行後の SSOX の Students テーブル](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="e13fe-551">移行が適用される前の名前列の型は [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) でした。</span><span class="sxs-lookup"><span data-stu-id="e13fe-551">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="e13fe-552">現在の名前列は `nvarchar(50)` です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-552">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="e13fe-553">列名は `FirstMidName` から `FirstName` に変わりました。</span><span class="sxs-lookup"><span data-stu-id="e13fe-553">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="e13fe-554">次のセクションでは、いくつかのステージでアプリをビルドします。その場合、コンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-554">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="e13fe-555">手順では、アプリをビルドするタイミングを指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-555">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="e13fe-556">Student エンティティの更新</span><span class="sxs-lookup"><span data-stu-id="e13fe-556">Student entity update</span></span>

![Student エンティティ](complex-data-model/_static/student-entity.png)

<span data-ttu-id="e13fe-558">以下のコードを使用して、*Models/Student.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-558">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="e13fe-559">Required 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-559">The Required attribute</span></span>

<span data-ttu-id="e13fe-560">`Required` 属性では、名前プロパティの必須フィールドを作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-560">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="e13fe-561">値の型 (`DateTime`、`int`、`double` など) などの null 非許容型では `Required` 属性は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-561">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="e13fe-562">null にできない型は自動的に必須フィールドとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-562">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="e13fe-563">`Required` 属性は、`StringLength` 属性の最小長パラメーターに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-563">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="e13fe-564">Display 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-564">The Display attribute</span></span>

<span data-ttu-id="e13fe-565">`Display` 属性では、テキスト ボックスのキャプションを "First Name"、"Last Name"、"Full Name"、"Enrollment Date" にするよう指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-565">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="e13fe-566">既定のキャプションには、"Lastname" のように、単語を区切るスペースはありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-566">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="e13fe-567">FullName 集計プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-567">The FullName calculated property</span></span>

<span data-ttu-id="e13fe-568">`FullName` は集計プロパティであり、2 つの別のプロパティを連結して作成される値を返します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-568">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="e13fe-569">`FullName` を設定することはできません。get アクセサーのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-569">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="e13fe-570">データベースには `FullName` 列は作成されません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-570">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="e13fe-571">Instructor エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-571">Create the Instructor Entity</span></span>

![Instructor エンティティ](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="e13fe-573">以下のコードを使用して、*Models/Instructor.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-573">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="e13fe-574">複数の属性を 1 行に配置することができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-574">Multiple attributes can be on one line.</span></span> <span data-ttu-id="e13fe-575">`HireDate` 属性は次のように記述できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-575">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="e13fe-576">CourseAssignments と OfficeAssignment ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-576">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="e13fe-577">`CourseAssignments` と `OfficeAssignment` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-577">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="e13fe-578">講師は任意の数のコースを担当できるため、`CourseAssignments` はコレクションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-578">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="e13fe-579">ナビゲーション プロパティに複数のエンティティが保持されている場合:</span><span class="sxs-lookup"><span data-stu-id="e13fe-579">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="e13fe-580">エンティティを追加、削除、更新できるリスト型である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-580">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="e13fe-581">ナビゲーション プロパティの型には次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-581">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="e13fe-582">`ICollection<T>` が指定されている場合は、EF Core で `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-582">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="e13fe-583">`CourseAssignment` エンティティについては、多対多リレーションシップのセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-583">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="e13fe-584">Contoso University のビジネス ルールには、講師は 1 つのオフィスのみを持つことができると示されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-584">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="e13fe-585">`OfficeAssignment` プロパティでは単一の `OfficeAssignment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-585">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="e13fe-586">オフィスが割り当てられていない場合、`OfficeAssignment` は null です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-586">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="e13fe-587">OfficeAssignment エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-587">Create the OfficeAssignment entity</span></span>

![OfficeAssignment エンティティ](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="e13fe-589">以下のコードを使用して、*Models/OfficeAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-589">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="e13fe-590">Key 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-590">The Key attribute</span></span>

<span data-ttu-id="e13fe-591">`[Key]` 属性は、プロパティ名が classnameID や ID 以外である場合に、主キー (PK) としてプロパティを識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-591">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="e13fe-592">`Instructor` エンティティと `OfficeAssignment` エンティティの間には一対ゼロまたは一対一のリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-592">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="e13fe-593">オフィスが割り当てられている講師についてのみ、オフィス割り当てが存在します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-593">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="e13fe-594">`OfficeAssignment` PK は、`Instructor` エンティティに対する外部キー (FK) でもあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-594">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="e13fe-595">EF Core では、以下の理由で、`OfficeAssignment` の PK として `InstructorID` を自動的に認識することはできません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-595">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="e13fe-596">`InstructorID` は、ID や classnameID の名前付け規則には従っていません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-596">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="e13fe-597">したがって、`Key` 属性は PK として `InstructorID` を識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-597">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="e13fe-598">列は依存リレーションシップに対するものであるため、既定では EF Core はキーを非データベース生成として扱います。</span><span class="sxs-lookup"><span data-stu-id="e13fe-598">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="e13fe-599">Instructor ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-599">The Instructor navigation property</span></span>

<span data-ttu-id="e13fe-600">`Instructor` エンティティの `OfficeAssignment` ナビゲーション プロパティは、以下の理由で null 許容型となります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-600">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="e13fe-601">参照型 (クラスが null 許容型である)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-601">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="e13fe-602">講師にオフィスが割り当てられていない可能性がある。</span><span class="sxs-lookup"><span data-stu-id="e13fe-602">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="e13fe-603">`OfficeAssignment` エンティティには、以下の理由で null 非許容の `Instructor` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-603">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="e13fe-604">`InstructorID` は null 非許容です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-604">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="e13fe-605">オフィス割り当ては講師なしでは存在できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-605">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="e13fe-606">`Instructor` エンティティに関連する `OfficeAssignment` エンティティがある場合、各エンティティにはそのナビゲーション プロパティの別のエンティティへの参照があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-606">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="e13fe-607">`[Required]` 属性を `Instructor` ナビゲーション プロパティに適用することができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-607">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="e13fe-608">上のコードは、関連する講師が存在する必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-608">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="e13fe-609">`InstructorID` 外部キー (PK でもある) が null 非許容であるため、上のコードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-609">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="e13fe-610">Course エンティティを変更する</span><span class="sxs-lookup"><span data-stu-id="e13fe-610">Modify the Course Entity</span></span>

![Course エンティティ](complex-data-model/_static/course-entity.png)

<span data-ttu-id="e13fe-612">以下のコードを使用して、*Models/Course.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-612">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="e13fe-613">`Course` エンティティには外部キー (FK) プロパティ `DepartmentID` があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-613">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="e13fe-614">`DepartmentID` は関連する `Department` エンティティを指します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-614">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="e13fe-615">`Course` エンティティには `Department` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-615">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="e13fe-616">EF Core では、モデルに関連エンティティのナビゲーション プロパティがある場合、データ モデルの FK プロパティは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-616">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="e13fe-617">EF Core は、必要に応じて、データベースで自動的に FK を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-617">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="e13fe-618">EF Core は、自動的に作成された FK に対して、[シャドウ プロパティ](/ef/core/modeling/shadow-properties)を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-618">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="e13fe-619">データ モデルに FK がある場合は、更新をより簡単かつ効率的に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-619">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="e13fe-620">たとえば、FK プロパティ `DepartmentID` が含まれて*いない* モデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-620">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="e13fe-621">Course エンティティが編集用にフェッチされた場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-621">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="e13fe-622">`Department` エンティティは、明示的に読み込まれない場合、null となります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-622">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="e13fe-623">Course エンティティを更新するには、`Department` エンティティを最初にフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-623">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="e13fe-624">FK エンティティ `DepartmentID` がデータ モデルに含まれている場合は、更新前に `Department` エンティティをフェッチする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-624">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="e13fe-625">DatabaseGenerated 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-625">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="e13fe-626">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 属性では、PK をデータベースで生成するのではなく、アプリケーションで提供するように指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-626">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="e13fe-627">既定では、EF Core は、PK 値が DB で生成されることを前提とします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-627">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="e13fe-628">通常は、PK 値を DB で生成するのが最適です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-628">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="e13fe-629">`Course` エンティティの場合、PK はユーザーが指定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-629">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="e13fe-630">たとえば、数学科の場合は 1000 シリーズ、英文科の場合は 2000 シリーズなどのコース番号となります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-630">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="e13fe-631">`DatabaseGenerated` 属性は、既定値を生成する場合にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-631">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="e13fe-632">たとえば、DB では、行が作成または更新された日付を記録するための日付フィールドを自動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-632">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="e13fe-633">詳細については、「[生成される値](/ef/core/modeling/generated-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-633">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e13fe-634">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-634">Foreign key and navigation properties</span></span>

<span data-ttu-id="e13fe-635">`Course` エンティティの外部キー (FK) プロパティとナビゲーション プロパティには、以下のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-635">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="e13fe-636">コースが 1 つの学科に割り当てられています。したがって、`DepartmentID` FK と `Department` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-636">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="e13fe-637">コースには任意の数の学生が登録できるため、`Enrollments` ナビゲーション プロパティはコレクションとなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-637">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="e13fe-638">コースは複数の講師が担当する場合があるため、`CourseAssignments` ナビゲーション プロパティはコレクションとなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-638">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="e13fe-639">`CourseAssignment` については、[後で](#many-to-many-relationships)説明します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-639">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="e13fe-640">Department エンティティを作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-640">Create the Department entity</span></span>

![Department エンティティ](complex-data-model/_static/department-entity.png)

<span data-ttu-id="e13fe-642">以下のコードを使用して、*Models/Department.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-642">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="e13fe-643">Column 属性</span><span class="sxs-lookup"><span data-stu-id="e13fe-643">The Column attribute</span></span>

<span data-ttu-id="e13fe-644">これまでは、`Column` 属性が列名のマッピングを変更するために使用されました。</span><span class="sxs-lookup"><span data-stu-id="e13fe-644">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="e13fe-645">`Department` エンティティのコードでは、`Column` 属性は SQL データ型のマッピングを変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-645">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="e13fe-646">`Budget` 列は、次のように、DB の SQL Server の money 型を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-646">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="e13fe-647">通常、列マッピングは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-647">Column mapping is generally not required.</span></span> <span data-ttu-id="e13fe-648">通常、EF Core はプロパティの CLR 型に基づいて、適切な SQL Server のデータ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-648">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="e13fe-649">CLR `decimal` 型は SQL Server の `decimal` 型にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-649">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="e13fe-650">`Budget` は通貨用であり、通貨には money データ型がより適しています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-650">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e13fe-651">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-651">Foreign key and navigation properties</span></span>

<span data-ttu-id="e13fe-652">FK およびナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-652">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="e13fe-653">学科には管理者が存在する場合とそうでない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-653">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="e13fe-654">管理者は常に講師です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-654">An administrator is always an instructor.</span></span> <span data-ttu-id="e13fe-655">したがって、`InstructorID` プロパティは `Instructor` エンティティに対する FK として含まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-655">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="e13fe-656">ナビゲーション プロパティは `Administrator` という名前ですが、`Instructor` エンティティを保持します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-656">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="e13fe-657">上のコードの疑問符 (?) は、プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-657">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="e13fe-658">学科には複数のコースがある場合があるため、Courses ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-658">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="e13fe-659">メモ:規則により、EF Core では null 非許容の FK と多対多リレーションシップに対して連鎖削除が有効になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-659">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="e13fe-660">連鎖削除では、循環連鎖削除規則が適用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-660">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="e13fe-661">循環連鎖削除規則が適用されると、移行の追加時に例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-661">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="e13fe-662">たとえば、`Department.InstructorID` プロパティが null 許容として定義されなかった場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-662">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="e13fe-663">EF Core は、講師が削除されたときに学科を削除するように連鎖削除規則を構成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-663">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="e13fe-664">講師が削除されたときに学科を削除するのは、意図した動作ではありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-664">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="e13fe-665">次の fluent API で、連鎖の代わりに制限規則を設定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-665">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="e13fe-666">上のコードでは、学科と講師のリレーションシップの連鎖削除が無効になります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-666">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="e13fe-667">Enrollment エンティティを更新する</span><span class="sxs-lookup"><span data-stu-id="e13fe-667">Update the Enrollment entity</span></span>

<span data-ttu-id="e13fe-668">1 件の登録レコードは、1 人の学生が受講する 1 つのコースに対するものです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-668">An enrollment record is for one course taken by one student.</span></span>

![Enrollment エンティティ](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="e13fe-670">以下のコードを使用して、*Models/Enrollment.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-670">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e13fe-671">外部キー プロパティとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-671">Foreign key and navigation properties</span></span>

<span data-ttu-id="e13fe-672">FK プロパティとナビゲーション プロパティには、次のリレーションシップが反映されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-672">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="e13fe-673">登録レコードは 1 つのコースに対するものであるため、`CourseID` FK プロパティと `Course` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-673">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="e13fe-674">登録レコードは 1 人の学生に対するものであるため、`StudentID` FK プロパティと `Student` ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-674">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="e13fe-675">多対多リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="e13fe-675">Many-to-Many Relationships</span></span>

<span data-ttu-id="e13fe-676">`Student` エンティティと `Course` エンティティの間には多対多リレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-676">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="e13fe-677">`Enrollment` エンティティは、データベースで*ペイロードがある*多対多結合テーブルとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-677">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="e13fe-678">"ペイロードがある" とは、`Enrollment` テーブルに、結合テーブルの FK 以外に追加データが含まれていることを意味します (ここでは PK と `Grade`)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-678">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="e13fe-679">次の図は、エンティティ図でこれらのリレーションシップがどのようになるかを示しています</span><span class="sxs-lookup"><span data-stu-id="e13fe-679">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="e13fe-680">(この図は、EF 6.x 用の [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) を使用して生成されたものです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-680">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="e13fe-681">このチュートリアルでは図は作成しません)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-681">Creating the diagram isn't part of the tutorial.)</span></span>

![Student と Course の多対多リレーションシップ](complex-data-model/_static/student-course.png)

<span data-ttu-id="e13fe-683">各リレーションシップ線の一方の端に 1 が、もう一方の端にアスタリスク (\*) があり、1 対多リレーションシップであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-683">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="e13fe-684">`Enrollment` テーブルに成績情報が含まれていない場合、含める必要があるのは 2 つの FK (`CourseID` と `StudentID`) のみです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-684">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="e13fe-685">ペイロードがない多対多結合テーブルは純粋結合テーブル (PJT) と呼ばれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-685">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="e13fe-686">`Instructor` および `Course` エンティティには、純粋結合テーブルを使用する多対多リレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-686">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="e13fe-687">メモ:EF 6.x では多対多リレーションシップの暗黙の結合テーブルがサポートされますが、EF Core ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-687">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="e13fe-688">詳細については、「[Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)」 (EF Core 2.0 の多対多リレーションシップ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-688">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="e13fe-689">CourseAssignment エンティティ</span><span class="sxs-lookup"><span data-stu-id="e13fe-689">The CourseAssignment entity</span></span>

![CourseAssignment エンティティ](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="e13fe-691">以下のコードを使用して、*Models/CourseAssignment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-691">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="e13fe-692">講師対コース</span><span class="sxs-lookup"><span data-stu-id="e13fe-692">Instructor-to-Courses</span></span>

![講師対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="e13fe-694">講師対コースの多対多リレーションシップは、</span><span class="sxs-lookup"><span data-stu-id="e13fe-694">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="e13fe-695">エンティティ セットで表される必要がある結合テーブルが必要です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-695">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="e13fe-696">純粋結合テーブル (ペイロードがないテーブル) です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-696">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="e13fe-697">結合エンティティには `EntityName1EntityName2` という名前が付けるのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-697">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="e13fe-698">たとえば、このパターンを使用する講師対コースの結合テーブルは `CourseInstructor` となります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-698">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="e13fe-699">ただし、リレーションシップを説明する名前を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-699">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="e13fe-700">データ モデルは始めは単純なものであっても大きくなります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-700">Data models start out simple and grow.</span></span> <span data-ttu-id="e13fe-701">ペイロードがない結合 (PJT) は頻繁に進化し、ペイロードが含まれるようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-701">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="e13fe-702">最初にわかりやすいエンティティ名を付けておけば、結合テーブルが変更されたときに名前を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-702">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="e13fe-703">結合エンティティでは、ビジネス ドメインに独自の自然な (場合によっては 1 単語の) 名前を指定することが理想的です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-703">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="e13fe-704">たとえば、Books と Customers は Ratings という結合エンティティでリンクできます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-704">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="e13fe-705">講師対コースの多対多リレーションシップの場合、`CourseAssignment` は `CourseInstructor` より優先されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-705">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="e13fe-706">複合キー</span><span class="sxs-lookup"><span data-stu-id="e13fe-706">Composite key</span></span>

<span data-ttu-id="e13fe-707">FK は null 非許容です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-707">FKs are not nullable.</span></span> <span data-ttu-id="e13fe-708">`CourseAssignment` の 2 つの FK (`InstructorID` と `CourseID`) を組み合わせて使用し、`CourseAssignment` テーブルの各行を一意に識別します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-708">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="e13fe-709">`CourseAssignment` には専用の PK は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-709">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="e13fe-710">`InstructorID` および `CourseID` プロパティは複合 PK として機能します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-710">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="e13fe-711">EF Core に複合 PK を指定する唯一の方法は、*fluent API* を使用することです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-711">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="e13fe-712">次のセクションでは、複合 PK の構成方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-712">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="e13fe-713">複合キーでは、必ず次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-713">The composite key ensures:</span></span>

* <span data-ttu-id="e13fe-714">1 つのコースに対して複数の行が許可される。</span><span class="sxs-lookup"><span data-stu-id="e13fe-714">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="e13fe-715">1 人の講師に対して複数の行が許可される。</span><span class="sxs-lookup"><span data-stu-id="e13fe-715">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="e13fe-716">同じ講師とコースに対して複数の行が許可されない。</span><span class="sxs-lookup"><span data-stu-id="e13fe-716">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="e13fe-717">`Enrollment` 結合エンティティでは独自の PK を定義するため、このような重複が考えられます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-717">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="e13fe-718">このような重複を防ぐには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-718">To prevent such duplicates:</span></span>

* <span data-ttu-id="e13fe-719">FK フィールドに一意のインデックスを追加する。または</span><span class="sxs-lookup"><span data-stu-id="e13fe-719">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="e13fe-720">`CourseAssignment` と同様の複合主キーを使用して、`Enrollment` を構成する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-720">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="e13fe-721">詳細については、「[インデックス](/ef/core/modeling/indexes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-721">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="e13fe-722">DB コンテキストを更新する</span><span class="sxs-lookup"><span data-stu-id="e13fe-722">Update the DB context</span></span>

<span data-ttu-id="e13fe-723">次の強調表示されているコードを *Data/SchoolContext.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-723">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="e13fe-724">上のコードでは新しいエンティティが追加され、`CourseAssignment` エンティティの複合 PK が構成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-724">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="e13fe-725">属性の代わりに fluent API を使用する</span><span class="sxs-lookup"><span data-stu-id="e13fe-725">Fluent API alternative to attributes</span></span>

<span data-ttu-id="e13fe-726">上のコードの `OnModelCreating` メソッドでは、*fluent API* を使用して EF Core の動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-726">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="e13fe-727">API は "fluent" と呼ばれます。これは、多くの場合、一連のメソッド呼び出しを単一のステートメントにまとめて使用されるためです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-727">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="e13fe-728">[次のコード](/ef/core/modeling/#use-fluent-api-to-configure-a-model)は fluent API の例です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-728">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="e13fe-729">このチュートリアルでは、属性で実行できない DB マッピングの場合にのみ、fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-729">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="e13fe-730">ただし、fluent API では、属性で実行できる書式設定、検証、マッピング規則のほとんどを指定できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-730">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="e13fe-731">`MinimumLength` などの一部の属性は fluent API で適用できません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-731">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="e13fe-732">`MinimumLength` ではスキーマを変更せず、最小長の検証規則のみを適用します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-732">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="e13fe-733">一部の開発者は fluent API のみを使用することを選ぶため、エンティティ クラスを "クリーン" な状態に保つことができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-733">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="e13fe-734">属性と fluent API を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-734">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="e13fe-735">(複合 PK を指定して) fluent API でのみ実行できる構成がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-735">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="e13fe-736">属性 (`MinimumLength`) でのみ実行できる構成もいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-736">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="e13fe-737">次のように、fluent API または属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-737">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="e13fe-738">これら 2 つの方法のいずれかを選択する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-738">Choose one of these two approaches.</span></span>
* <span data-ttu-id="e13fe-739">できるだけ一貫性を保つために選択した方法を使用する。</span><span class="sxs-lookup"><span data-stu-id="e13fe-739">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="e13fe-740">このチュートリアルで使用する属性のいくつかは、次の用途に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-740">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="e13fe-741">検証のみ (`MinimumLength` など)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-741">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="e13fe-742">EF Core 構成のみ (`HasKey` など)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-742">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="e13fe-743">検証と EF Core の構成 (`[StringLength(50)]` など)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-743">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="e13fe-744">属性と fluent API の詳細については、「[構成の方法](/ef/core/modeling/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-744">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="e13fe-745">リレーションシップを示すエンティティ図</span><span class="sxs-lookup"><span data-stu-id="e13fe-745">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="e13fe-746">次の図では、完成した School モデルに対して EF Power Tools で作成される図を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-746">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![エンティティ図](complex-data-model/_static/diagram.png)

<span data-ttu-id="e13fe-748">上の図には以下が示されています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-748">The preceding diagram shows:</span></span>

* <span data-ttu-id="e13fe-749">いくつかの一対多リレーションシップの線 (1 対 \*)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-749">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="e13fe-750">`Instructor` エンティティと `OfficeAssignment` エンティティの間の一対ゼロまたは一対一リレーションシップの線 (1 対 0..1)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-750">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="e13fe-751">`Instructor` エンティティと `Department` エンティティの間のゼロ対一またはゼロ対多リレーションシップの線 (1 対 0..1)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-751">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="e13fe-752">テスト データで DB をシードする</span><span class="sxs-lookup"><span data-stu-id="e13fe-752">Seed the DB with Test Data</span></span>

<span data-ttu-id="e13fe-753">*Data/DbInitializer.cs* のコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-753">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="e13fe-754">上のコードでは、新しいエンティティのシード データが提供されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-754">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="e13fe-755">このコードのほとんどで新しいエンティティ オブジェクトが作成され、サンプル データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-755">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="e13fe-756">サンプル データはテストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-756">The sample data is used for testing.</span></span> <span data-ttu-id="e13fe-757">多対多結合テーブルがシードされる方法の例については、`Enrollments` と `CourseAssignments` を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-757">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="e13fe-758">移行を追加する</span><span class="sxs-lookup"><span data-stu-id="e13fe-758">Add a migration</span></span>

<span data-ttu-id="e13fe-759">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-759">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-760">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-760">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-761">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-761">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="e13fe-762">上のコマンドは、考えられるデータ損失に関する警告を表示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-762">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="e13fe-763">`database update` コマンドを実行すると、次のエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-763">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="e13fe-764">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="e13fe-764">Apply the migration</span></span>

<span data-ttu-id="e13fe-765">既存のデータベースができたので、将来の変更を適用する方法について検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-765">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="e13fe-766">このチュートリアルでは、2 つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-766">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="e13fe-767">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-767">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="e13fe-768">[移行を既存のデータベースに適用する](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="e13fe-768">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="e13fe-769">この方法はより複雑で時間がかかりますが、実際の運用環境では推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-769">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="e13fe-770">**注**:これは、チュートリアルのオプションのセクションです。</span><span class="sxs-lookup"><span data-stu-id="e13fe-770">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="e13fe-771">削除と再作成の手順を行い、このセクションはスキップしてもかまいません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-771">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="e13fe-772">このセクションの手順に従う場合は、削除と再作成の手順を行わないでください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-772">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="e13fe-773">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="e13fe-773">Drop and re-create the database</span></span>

<span data-ttu-id="e13fe-774">更新された `DbInitializer` のコードでは、新しいエンティティのシード データを追加します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-774">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="e13fe-775">EF Core に新しい DB を強制的に作成させるには、DB を削除して更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-775">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e13fe-776">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e13fe-776">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e13fe-777">**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-777">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="e13fe-778">PMC から `Get-Help about_EntityFrameworkCore` を実行してヘルプ情報を入手します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-778">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e13fe-779">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e13fe-779">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e13fe-780">コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-780">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="e13fe-781">プロジェクト フォルダーには *Startup.cs* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-781">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="e13fe-782">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-782">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="e13fe-783">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-783">Run the app.</span></span> <span data-ttu-id="e13fe-784">アプリを実行すると `DbInitializer.Initialize` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-784">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="e13fe-785">`DbInitializer.Initialize` は新しい DB を設定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-785">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="e13fe-786">SSOX で DB を開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-786">Open the DB in SSOX:</span></span>

* <span data-ttu-id="e13fe-787">SSOX が既に開いている場合は、 **[更新]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-787">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="e13fe-788">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-788">Expand the **Tables** node.</span></span> <span data-ttu-id="e13fe-789">作成されたテーブルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-789">The created tables are displayed.</span></span>

![SSOX のテーブル](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="e13fe-791">**CourseAssignment** テーブルを確認します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-791">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="e13fe-792">**CourseAssignment** テーブルを右クリックして、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-792">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="e13fe-793">**CourseAssignment** テーブルにデータが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-793">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX の CourseAssignment データ](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="e13fe-795">移行を既存のデータベースに適用する</span><span class="sxs-lookup"><span data-stu-id="e13fe-795">Apply the migration to the existing database</span></span>

<span data-ttu-id="e13fe-796">このセクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="e13fe-796">This section is optional.</span></span> <span data-ttu-id="e13fe-797">以下の手順は、前の「[データベースを削除して再作成する](#drop)」セクションをスキップした場合にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-797">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="e13fe-798">既存のデータで移行が実行されている場合、既存のデータでは満たされない FK 制約が存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-798">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="e13fe-799">運用データを使用する場合は、既存のデータを移行するための手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e13fe-799">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="e13fe-800">このセクションでは、FK 制約違反の修正例を示します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-800">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="e13fe-801">これらのコードをバックアップせずに変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-801">Don't make these code changes without a backup.</span></span> <span data-ttu-id="e13fe-802">前のセクションを完了し、データベースを更新した場合は、これらのコードを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="e13fe-802">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="e13fe-803">*{timestamp}_ComplexDataModel.cs* ファイルには次のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e13fe-803">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="e13fe-804">上のコードでは、null 非許容の `DepartmentID` FK が `Course` テーブルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-804">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="e13fe-805">前のチュートリアルの DB には `Course` の行が含まれるため、テーブルを移行して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-805">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="e13fe-806">既存のデータを使用して `ComplexDataModel` の移行を実行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-806">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="e13fe-807">コードを変更して、新しい列 (`DepartmentID`) に既定値を設定します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-807">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="e13fe-808">"Temp" という名前の偽の学科を作成し、既定の学科として機能するようにします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-808">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="e13fe-809">外部キー制約を修正する</span><span class="sxs-lookup"><span data-stu-id="e13fe-809">Fix the foreign key constraints</span></span>

<span data-ttu-id="e13fe-810">`ComplexDataModel` クラスの `Up` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-810">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="e13fe-811">*{timestamp}_ComplexDataModel.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-811">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="e13fe-812">`DepartmentID` 列を `Course` テーブルに追加するコードの行をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="e13fe-812">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="e13fe-813">次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-813">Add the following highlighted code.</span></span> <span data-ttu-id="e13fe-814">新しいコードが `.CreateTable( name: "Department"` ブロックの後に配置されます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-814">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="e13fe-815">前述の変更に伴い、既存の `Course` 行が、`ComplexDataModel` `Up` メソッドの実行後に "Temp" 学科に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="e13fe-815">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="e13fe-816">運用アプリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="e13fe-816">A production app would:</span></span>

* <span data-ttu-id="e13fe-817">コードまたはスクリプトを組み込み、`Department` 行と関連する `Course` 行を新しい `Department` 行に追加します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-817">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="e13fe-818">`Course.DepartmentID` の既定値や "Temp" 学科は使用しません。</span><span class="sxs-lookup"><span data-stu-id="e13fe-818">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="e13fe-819">次のチュートリアルでは関連するデータについて説明します。</span><span class="sxs-lookup"><span data-stu-id="e13fe-819">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e13fe-820">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e13fe-820">Additional resources</span></span>

* [<span data-ttu-id="e13fe-821">このチュートリアルの YouTube バージョン (パート 1)</span><span class="sxs-lookup"><span data-stu-id="e13fe-821">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="e13fe-822">このチュートリアルの YouTube バージョン (パート 2)</span><span class="sxs-lookup"><span data-stu-id="e13fe-822">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> <span data-ttu-id="e13fe-823">[前へ](xref:data/ef-rp/migrations)
> [次へ](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="e13fe-823">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end