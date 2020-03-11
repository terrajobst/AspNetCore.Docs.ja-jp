---
title: ASP.NET Core の Razor ページと EF Core - データ モデル - 5/8
author: rick-anderson
description: このチュートリアルでは、エンティティとリレーションシップをさらに追加し、書式設定、検証、マッピングの規則を指定してデータ モデルをカスタマイズします。
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650210"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>ASP.NET Core の Razor ページと EF Core - データ モデル - 5/8

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

前のチュートリアルでは、3 つのエンティティで構成された基本的なデータ モデルを使用して作業を行いました。 このチュートリアルでは、次の作業を行います。

* エンティティとリレーションシップをさらに追加する。
* 書式設定、検証、データベース マッピングの規則を指定して、データ モデルをカスタマイズする。

完成したデータ モデルは、次の図のようになります。

![エンティティ図](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Student エンティティ

![Student エンティティ](complex-data-model/_static/student-entity.png)

*Models/Student.cs* のコードを次のコードに置き換えます。

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

前のコードでは、`FullName` プロパティが追加され、既存のプロパティに次の属性が追加されます。

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>FullName 集計プロパティ

`FullName` は集計プロパティであり、2 つの別のプロパティを連結して作成される値を返します。 `FullName` を設定することはできないので、get アクセサーのみが含まれます。 データベースには `FullName` 列は作成されません。

### <a name="the-datatype-attribute"></a>DataType 属性

```csharp
[DataType(DataType.Date)]
```

学生の登録日について、日付のみが関係しますが、現在はすべての Web ページに日付と共に時刻が表示されています。 データ注釈属性を使用すれば、1 つのコードを変更するだけで、データが表示されるすべてのページの表示形式を修正できます。 

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 属性では、データベースの組み込み型よりも具体的なデータ型を指定します。 ここでは、日付と時刻ではなく、日付のみを表示する必要があります。 [DataType 列挙型](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くのデータ型のために用意されています。また、`DataType` 属性を使用して、アプリで型固有の機能を自動的に提供することもできます。 次に例を示します。

* `mailto:` リンクは `DataType.EmailAddress` に対して自動的に作成されます。
* ほとんどのブラウザーでは、`DataType.Date` に日付セレクターが提供されます。

`DataType` 属性では、HTML 5 の `data-` (データ ダッシュと読みます) 属性が出力されます。 `DataType` 属性では検証は提供されません。

### <a name="the-displayformat-attribute"></a>DisplayFormat 属性

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` は、表示される日付の書式を指定しません。 既定で、日付フィールドはサーバーの [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) に基づき、既定の書式に従って表示されます。

`DisplayFormat` 属性は、日付の形式を明示的に指定するために使用されます。 `ApplyFormatInEditMode` 設定では、書式設定を編集 UI にも適用する必要があることを指定します。 一部のフィールドでは `ApplyFormatInEditMode` を使用できません。 たとえば、通貨記号は一般的に編集テキスト ボックスには表示できません。

`DisplayFormat` 属性は単独で使用できます。 一般的には、`DataType` 属性を `DisplayFormat` 属性と一緒に使用することをお勧めします。 `DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。 `DataType` 属性には、`DisplayFormat` では得られない以下のような利点があります。

* ブラウザーで HTML5 機能を有効にすることができます。 たとえば、カレンダー コントロール、ロケールに適した通貨記号、メール リンク、クライアント側の入力検証を表示します。
* 既定では、ブラウザーで、ロケールに基づいて正しい書式を使用してデータがレンダリングされます。

詳細については、[\<入力> タグ ヘルパーに関するドキュメント](xref:mvc/views/working-with-forms#the-input-tag-helper)を参照してください。

### <a name="the-stringlength-attribute"></a>StringLength 属性

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

データ検証規則と検証エラー メッセージは、属性を使用して指定できます。 [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 属性では、データ フィールドで使用できる最小文字長と最大文字長を指定します。 示されているコードでは、名前で使用可能な文字数が 50 字以下に制限されます。 文字列の最小長を設定する例は、[後で](#the-required-attribute)示します。

また、`StringLength` 属性ではクライアント側とサーバー側の検証も提供されます。 最小値は、データベース スキーマに影響しません。

`StringLength` 属性では、ユーザーが名前に空白を入力しないようにすることはできません。 [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 属性は、入力に制限を適用するために使用できます。 たとえば、次のコードでは、最初の文字を大文字にし、残りの文字をアルファベット順にすることを要求します。

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**SQL Server オブジェクト エクスプローラー** (SSOX) で、**Student** テーブルをダブルクリックして、Student テーブル デザイナーを開きます。

![移行前の SSOX の Students テーブル](complex-data-model/_static/ssox-before-migration.png)

上の図には `Student` テーブルのスキーマが表示されています。 名前フィールドは `nvarchar(MAX)` 型です。 このチュートリアルの後半で移行を作成して適用すると、文字列長属性の結果として名前フィールドは `nvarchar(50)` になります。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite ツールで、`Student` テーブルの列の定義を調べます。 名前フィールドは `Text` 型です。 最初の名前フィールドの名前は `FirstMidName` であることに注意してください。 次のセクションでは、その列の名前を `FirstName` に変更します。

---

### <a name="the-column-attribute"></a>Column 属性

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

属性で、データベースへのクラスとプロパティのマッピング方法を制御することができます。 `Student` モデルでは、`Column` 属性を使用して、`FirstMidName` プロパティの名前をデータベースの "FirstName" にマッピングします。

データベースが作成されたときに、列名でモデルのプロパティ名が使用されます (`Column` 属性が使用されている場合を除く)。 `Student` モデルでは名フィールドに対して `FirstMidName` が使用されます。これは、フィールドにミドル ネームも含まれている場合があるためです。

`[Column]` 属性により、データ モデル内の `Student.FirstMidName` が、`Student` テーブルの `FirstName` 列にマップされます。 `Column` 属性を追加すると、`SchoolContext` をサポートするモデルが変更されます。 `SchoolContext` をサポートするモデルはデータベースと一致しなくなります。 そのような不一致は、このチュートリアルの後半で移行を追加することによって解決されます。

### <a name="the-required-attribute"></a>Required 属性

```csharp
[Required]
```

`Required` 属性では、名前プロパティの必須フィールドを作成します。 値の型 (例: `DateTime`、`int`、`double`) などの null 非許容型では、`Required` 属性は必要ありません。 null にできない型は自動的に必須フィールドとして扱われます。

`MinimumLength` を適用するには、`Required` 属性を `MinimumLength` と共に使用する必要があります。

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` と `Required` を使用すると、空白で検証を満たすことができます。 文字列を完全に制御するには、`RegularExpression` 属性を使用します。

### <a name="the-display-attribute"></a>Display 属性

```csharp
[Display(Name = "Last Name")]
```

`Display` 属性では、テキスト ボックスのキャプションを "First Name"、"Last Name"、"Full Name"、"Enrollment Date" にするよう指定します。 既定のキャプションには、"Lastname" のように、単語を区切るスペースはありません。

### <a name="create-a-migration"></a>移行を作成する

アプリを実行して [Students] ページに移動します。 例外がスローされます。 `[Column]` 属性があるため、EF では `FirstName` という名前の列が検索されることが予想されますが、データベースの列名はまだ `FirstMidName` です。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

エラー メッセージは、次のようになります。

```
SqlException: Invalid column name 'FirstName'.
```

* PMC で、以下のコマンドを入力し、新しい移行を作成してデータベースを更新します。

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  これらの最初のコマンドでは、以下の警告メッセージが生成されます。

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  名前フィールドは現在、50 文字に制限されているため、警告が生成されます。 データベースの名前が 50 文字を超えた場合、51 番目から最後までの文字が失われます。

* SSOX で Student テーブルを開きます。

  ![移行後の SSOX の Students テーブル](complex-data-model/_static/ssox-after-migration.png)

  移行が適用される前の名前列の型は [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) でした。 現在の名前列は `nvarchar(50)` です。 列名は `FirstMidName` から `FirstName` に変わりました。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

エラー メッセージは、次のようになります。

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* プロジェクト フォルダーでコマンド ウィンドウを開きます。 以下のコマンドを入力し、新しい移行を作成してデータベースを更新します。

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  データベースの更新コマンドを実行すると、次の例のようなエラーが表示されます。

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

このチュートリアルでは、このエラーを回避する方法は、最初の移行を削除してから再作成することです。 詳しくは、[移行チュートリアル](xref:data/ef-rp/migrations)の先頭にある SQLite の警告に関する注意をご覧ください。

* *Migrations* フォルダーを削除します。
* 次のコマンドを実行して、データベースを削除し、新しい初期移行を作成して、移行を適用します。

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* SQLite ツールで Student テーブルを確認します。 FirstMidName だった列は FirstName になっています。

---

* アプリを実行して [Students] ページに移動します。
* 日付と共に時刻が入力および表示されていないことに注意してください。
* **[新規作成]** を選択し、50 文字を超える名前を入力してみます。

> [!Note]
> 次のセクションでは、いくつかのステージでアプリをビルドします。その場合、コンパイラ エラーが生成されます。 手順では、アプリをビルドするタイミングを指定します。

## <a name="the-instructor-entity"></a>Instructor エンティティ

![Instructor エンティティ](complex-data-model/_static/instructor-entity.png)

以下のコードを使用して、*Models/Instructor.cs* を作成します。

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

複数の属性を 1 行に配置することができます。 `HireDate` 属性は次のように記述できます。

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>ナビゲーション プロパティ

`CourseAssignments` と `OfficeAssignment` プロパティはナビゲーション プロパティです。

講師は任意の数のコースを担当できるため、`CourseAssignments` はコレクションとして定義されます。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

講師は最大で 1 つのオフィスを持つことができるので、`OfficeAssignment` プロパティには 1 つの `OfficeAssignment` エンティティが保持されます。 オフィスが割り当てられていない場合、`OfficeAssignment` は null です。

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>OfficeAssignment エンティティ

![OfficeAssignment エンティティ](complex-data-model/_static/officeassignment-entity.png)

以下のコードを使用して、*Models/OfficeAssignment.cs* を作成します。

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Key 属性

`[Key]` 属性は、プロパティ名が classnameID や ID 以外である場合に、主キー (PK) としてプロパティを識別するために使用されます。

`Instructor` エンティティと `OfficeAssignment` エンティティの間には一対ゼロまたは一対一のリレーションシップがあります。 オフィスが割り当てられている講師についてのみ、オフィス割り当てが存在します。 `OfficeAssignment` PK は、`Instructor` エンティティに対する外部キー (FK) でもあります。

`InstructorID` が ID または classnameID の名前付け規則に従っていないため、EF Core では、`InstructorID` が `OfficeAssignment` の PK として自動的に認識されません。 したがって、`Key` 属性は PK として `InstructorID` を識別するために使用されます。

```csharp
[Key]
public int InstructorID { get; set; }
```

列は依存リレーションシップに対するものであるため、既定では EF Core はキーを非データベース生成として扱います。

### <a name="the-instructor-navigation-property"></a>Instructor ナビゲーション プロパティ

特定の講師に対して `OfficeAssignment` 行が存在しない可能性があるため、`Instructor.OfficeAssignment` ナビゲーション プロパティは null でもかまいません。 講師にオフィスが割り当てられていない可能性がある。

外部キー `InstructorID` の型は `int` であり、null 非許容値型であるため、`OfficeAssignment.Instructor` ナビゲーション プロパティには常に講師のエンティティが含まれます。 オフィス割り当ては講師なしでは存在できません。

`Instructor` エンティティに関連する `OfficeAssignment` エンティティがある場合、各エンティティにはそのナビゲーション プロパティの別のエンティティへの参照があります。

## <a name="the-course-entity"></a>Course エンティティ

![Course エンティティ](complex-data-model/_static/course-entity.png)

以下のコードを使用して、*Models/Course.cs* を更新します。

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

`Course` エンティティには外部キー (FK) プロパティ `DepartmentID` があります。 `DepartmentID` は関連する `Department` エンティティを指します。 `Course` エンティティには `Department` ナビゲーション プロパティがあります。

EF Core では、モデルに関連エンティティのナビゲーション プロパティがある場合、データ モデルの外部キー プロパティは必要ありません。 EF Core は、必要に応じて、データベースで自動的に FK を作成します。 EF Core は、自動的に作成された FK に対して、[シャドウ プロパティ](/ef/core/modeling/shadow-properties)を作成します。 ただし、データ モデルに FK を明示的に含めると、更新をより簡単かつ効率的に行うことができます。 たとえば、FK プロパティ `DepartmentID` が含まれて*いない* モデルがあるとします。 Course エンティティが編集用にフェッチされた場合は、次のようになります。

* 明示的に読み込まれない場合、`Department` プロパティは null になります。
* Course エンティティを更新するには、`Department` エンティティを最初にフェッチする必要があります。

FK エンティティ `DepartmentID` がデータ モデルに含まれている場合は、更新前に `Department` エンティティをフェッチする必要はありません。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 属性

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 属性では、PK をデータベースで生成するのではなく、アプリケーションで提供するように指定します。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

既定では、EF Core では PK 値がデータベースによって生成されるものと想定されています。 通常は、データベースで生成するのが最善です。 `Course` エンティティの場合、PK はユーザーが指定します。 たとえば、数学科の場合は 1000 シリーズ、英文科の場合は 2000 シリーズなどのコース番号となります。

`DatabaseGenerated` 属性は、既定値を生成する場合にも使用できます。 たとえば、データベースでは、行が作成または更新された日付を記録するための日付フィールドを自動的に生成できます。 詳細については、「[生成される値](/ef/core/modeling/generated-properties)」を参照してください。

### <a name="foreign-key-and-navigation-properties"></a>外部キー プロパティとナビゲーション プロパティ

`Course` エンティティの外部キー (FK) プロパティとナビゲーション プロパティには、以下のリレーションシップが反映されます。

コースが 1 つの学科に割り当てられています。したがって、`DepartmentID` FK と `Department` ナビゲーション プロパティがあります。

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

コースには任意の数の学生が登録できるため、`Enrollments` ナビゲーション プロパティはコレクションとなります。

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

コースは複数の講師が担当する場合があるため、`CourseAssignments` ナビゲーション プロパティはコレクションとなります。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` については、[後で](#many-to-many-relationships)説明します。

## <a name="the-department-entity"></a>Department エンティティ

![Department エンティティ](complex-data-model/_static/department-entity.png)

以下のコードを使用して、*Models/Department.cs* を作成します。

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Column 属性

これまでは、`Column` 属性が列名のマッピングを変更するために使用されました。 `Department` エンティティのコードでは、`Column` 属性は SQL データ型のマッピングを変更するために使用されます。 `Budget` 列は、データベースでは次のように SQL Server の money 型を使用して定義されます。

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

通常、列マッピングは必要ありません。 EF Core では、プロパティの CLR 型に基づいて、適切な SQL Server のデータ型が選択されます。 CLR `decimal` 型は SQL Server の `decimal` 型にマップされます。 `Budget` は通貨用であり、通貨には money データ型がより適しています。

### <a name="foreign-key-and-navigation-properties"></a>外部キー プロパティとナビゲーション プロパティ

FK およびナビゲーション プロパティには、次のリレーションシップが反映されます。

* 学科には管理者が存在する場合とそうでない場合があります。
* 管理者は常に講師です。 したがって、`InstructorID` プロパティは `Instructor` エンティティに対する FK として含まれます。

ナビゲーション プロパティは `Administrator` という名前ですが、`Instructor` エンティティを保持します。

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

上のコードの疑問符 (?) は、プロパティが null 許容であることを示します。

学科には複数のコースがある場合があるため、Courses ナビゲーション プロパティがあります。

```csharp
public ICollection<Course> Courses { get; set; }
```

規則により、EF Core では null 非許容の FK と多対多リレーションシップに対して連鎖削除が有効になります。 この既定の動作では、循環連鎖削除規則が適用される可能性があります。 循環連鎖削除規則が適用されると、移行の追加時に例外が発生します。

たとえば、`Department.InstructorID` プロパティが null 非許容として定義されている場合、EF Core では連鎖削除規則が構成されます。 この場合、管理者として割り当てられた講師が削除されると、部門は削除されます。 このシナリオでは、制限規則がより合理的になります。 次の [fluent API](#fluent-api-alternative-to-attributes) では、制限規則が設定されて、連鎖削除が無効になります。

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Enrollment エンティティ

1 件の登録レコードは、1 人の学生が受講する 1 つのコースに対するものです。

![Enrollment エンティティ](complex-data-model/_static/enrollment-entity.png)

以下のコードを使用して、*Models/Enrollment.cs* を更新します。

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>外部キー プロパティとナビゲーション プロパティ

FK プロパティとナビゲーション プロパティには、次のリレーションシップが反映されます。

登録レコードは 1 つのコースに対するものであるため、`CourseID` FK プロパティと `Course` ナビゲーション プロパティがあります。

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

登録レコードは 1 人の学生に対するものであるため、`StudentID` FK プロパティと `Student` ナビゲーション プロパティがあります。

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>多対多リレーションシップ

`Student` エンティティと `Course` エンティティの間には多対多リレーションシップがあります。 `Enrollment` エンティティは、データベースで*ペイロードがある*多対多結合テーブルとして機能します。 "ペイロードがある" とは、`Enrollment` テーブルに、結合テーブルの FK 以外に追加データが含まれていることを意味します (ここでは PK と `Grade`)。

次の図は、エンティティ図でこれらのリレーションシップがどのようになるかを示しています (この図は、EF 6.x 用の [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) を使用して生成されたものです。 このチュートリアルでは図は作成しません)。

![Student と Course の多対多リレーションシップ](complex-data-model/_static/student-course.png)

各リレーションシップ線の一方の端に 1 が、もう一方の端にアスタリスク (*) があり、1 対多リレーションシップであることを示しています。

`Enrollment` テーブルに成績情報が含まれていない場合、含める必要があるのは 2 つの FK (`CourseID` と `StudentID`) のみです。 ペイロードがない多対多結合テーブルは純粋結合テーブル (PJT) と呼ばれる場合があります。

`Instructor` および `Course` エンティティには、純粋結合テーブルを使用する多対多リレーションシップがあります。

メモ:EF 6.x では多対多リレーションシップの暗黙の結合テーブルがサポートされますが、EF Core ではサポートされません。 詳細については、「[Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)」 (EF Core 2.0 の多対多リレーションシップ) を参照してください。

## <a name="the-courseassignment-entity"></a>CourseAssignment エンティティ

![CourseAssignment エンティティ](complex-data-model/_static/courseassignment-entity.png)

以下のコードを使用して、*Models/CourseAssignment.cs* を作成します。

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

講師とコースの多対多リレーションシップには結合テーブルが必要であり、その結合テーブルのエンティティは CourseAssignment です。

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

結合エンティティには `EntityName1EntityName2` という名前が付けるのが一般的です。 たとえば、このパターンを使用する講師対コースの結合テーブルは `CourseInstructor` になります。 ただし、リレーションシップを説明する名前を使用することをお勧めします。

データ モデルは始めは単純なものであっても大きくなります。 ペイロードがない結合テーブル (PJT) は、頻繁に更新されてペイロードが追加されます。 最初にわかりやすいエンティティ名を付けておけば、結合テーブルが変更されたときに名前を変更する必要はありません。 結合エンティティでは、ビジネス ドメインに独自の自然な (場合によっては 1 単語の) 名前を指定することが理想的です。 たとえば、Books と Customers は Ratings という結合エンティティでリンクできます。 講師対コースの多対多リレーションシップの場合、`CourseAssignment` は `CourseInstructor` より優先されます。

### <a name="composite-key"></a>複合キー

`CourseAssignment` の 2 つの FK (`InstructorID` と `CourseID`) を組み合わせて使用し、`CourseAssignment` テーブルの各行を一意に識別します。 `CourseAssignment` には専用の PK は必要ありません。 `InstructorID` および `CourseID` プロパティは複合 PK として機能します。 EF Core に複合 PK を指定する唯一の方法は、*fluent API* を使用することです。 次のセクションでは、複合 PK の構成方法を示します。

複合キーにより、次のことが保証されます。

* 1 つのコースに対して複数の行が許可される。
* 1 人の講師に対して複数の行が許可される。
* 同じ講師とコースに対して複数の行が許可されない。

`Enrollment` 結合エンティティでは独自の PK を定義するため、このような重複が考えられます。 このような重複を防ぐには、次のようにします。

* FK フィールドに一意のインデックスを追加する。または
* `CourseAssignment` と同様の複合主キーを使用して、`Enrollment` を構成する。 詳細については、「[インデックス](/ef/core/modeling/indexes)」を参照してください。

## <a name="update-the-database-context"></a>データベース コンテキストを更新する

次のコードを使用して *Data/SchoolContext.cs* を更新します。

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

上のコードでは新しいエンティティが追加され、`CourseAssignment` エンティティの複合 PK が構成されます。

## <a name="fluent-api-alternative-to-attributes"></a>属性の代わりに fluent API を使用する

上のコードの `OnModelCreating` メソッドでは、*fluent API* を使用して EF Core の動作を構成します。 API は "fluent" と呼ばれます。これは、多くの場合、一連のメソッド呼び出しを単一のステートメントにまとめて使用されるためです。 [次のコード](/ef/core/modeling/#use-fluent-api-to-configure-a-model)は fluent API の例です。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

このチュートリアルでは、属性で実行できないデータベース マッピングの場合にのみ、fluent API を使用します。 ただし、fluent API では、属性で実行できる書式設定、検証、マッピング規則のほとんどを指定できます。

`MinimumLength` などの一部の属性は fluent API で適用できません。 `MinimumLength` ではスキーマを変更せず、最小長の検証規則のみを適用します。

一部の開発者は fluent API のみを使用することを選ぶため、エンティティ クラスを "クリーン" な状態に保つことができます。 属性と fluent API を混在させることができます。 (複合 PK を指定して) fluent API でのみ実行できる構成がいくつかあります。 属性 (`MinimumLength`) でのみ実行できる構成もいくつかあります。 次のように、fluent API または属性を使用することをお勧めします。

* これら 2 つの方法のいずれかを選択する。
* できるだけ一貫性を保つために選択した方法を使用する。

このチュートリアルで使用する属性のいくつかは、次の用途に使用されます。

* 検証のみ (`MinimumLength` など)。
* EF Core 構成のみ (`HasKey` など)。
* 検証と EF Core の構成 (`[StringLength(50)]` など)。

属性と fluent API の詳細については、「[構成の方法](/ef/core/modeling/)」を参照してください。

## <a name="entity-diagram"></a>エンティティ図

次の図では、完成した School モデルに対して EF Power Tools で作成される図を示します。

![エンティティ図](complex-data-model/_static/diagram.png)

上の図には以下が示されています。

* いくつかの一対多リレーションシップの線 (1 対 \*)。
* `Instructor` エンティティと `OfficeAssignment` エンティティの間の一対ゼロまたは一対一リレーションシップの線 (1 対 0..1)。
* `Instructor` エンティティと `Department` エンティティの間のゼロ対一またはゼロ対多リレーションシップの線 (1 対 0..1)。

## <a name="seed-the-database"></a>データベースのシード

*Data/DbInitializer.cs* のコードを更新します。

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

上のコードでは、新しいエンティティのシード データが提供されます。 このコードのほとんどで新しいエンティティ オブジェクトが作成され、サンプル データが読み込まれます。 サンプル データはテストに使用されます。 多対多結合テーブルがシードされる方法の例については、`Enrollments` と `CourseAssignments` を参照してください。

## <a name="add-a-migration"></a>移行を追加する

プロジェクトをビルドします。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC で、次のコマンドを実行します。

```powershell
Add-Migration ComplexDataModel
```

上のコマンドは、考えられるデータ損失に関する警告を表示します。

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

`database update` コマンドを実行すると、次のエラーが生成されます。

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

次のセクションでは、このエラーの対処方法を説明します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

移行を追加して `database update` コマンドを実行すると、次のエラーが生成されます。

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

次のセクションでは、このエラーの回避方法を説明します。

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>移行を適用するか、削除して再作成する

既存のデータベースができたので、変更を適用する方法について検討する必要があります。 このチュートリアルでは、2 つの方法を示します。

* [データベースを削除して再作成する](#drop)。 SQLite を使用している場合は、このセクションを選択します。
* [移行を既存のデータベースに適用する](#applyexisting)。 このセクションの手順は SQL Server にのみ使用でき、**SQLite では使用できません**。 

どちらの選択肢も SQL Server で機能します。 移行適用方法はより複雑で時間がかかりますが、実際の運用環境では推奨される方法です。 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>データベースを削除して再作成する

SQL Server を使用していて、次のセクションの移行適用方法を実行する場合は、[このセクションをスキップします](#apply-the-migration)。

EF Core に新しいデータベースを強制的に作成させるには、データベースを削除して更新します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。

  ```powershell
  Drop-Database
  ```

* *Migrations* フォルダーを削除し、次のコマンドを実行します。

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。 プロジェクト フォルダーには、*ContosoUniversity.csproj* ファイルが含まれています。

* 次のコマンドを実行します。

  ```dotnetcli
  dotnet ef database drop --force
  ```

* *Migrations* フォルダーを削除し、次のコマンドを実行します。

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

アプリを実行します。 アプリを実行すると `DbInitializer.Initialize` メソッドが実行されます。 `DbInitializer.Initialize` では、新しいデータベースが設定されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

SSOX でデータベースを開きます。

* SSOX が既に開いている場合は、 **[更新]** ボタンをクリックします。
* **[Tables]\(テーブル\)** ノードを展開します。 作成されたテーブルが表示されます。

  ![SSOX のテーブル](complex-data-model/_static/ssox-tables.png)

* **CourseAssignment** テーブルを確認します。

  * **CourseAssignment** テーブルを右クリックして、 **[データの表示]** を選択します。
  * **CourseAssignment** テーブルにデータが含まれていることを確認します。

  ![SSOX の CourseAssignment データ](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite ツールを使用してデータベースを確認します。

* 新しいテーブルと列。
* テーブル内のシードされたデータ (**CourseAssignment**テーブルなど)。

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>移行を適用する

このセクションは省略可能です。 以下の手順は、SQL Server LocalDB に対してだけ、前の「[データベースを削除して再作成する](#drop)」セクションをスキップした場合にのみ、使用できます。

既存のデータで移行が実行されている場合、既存のデータでは満たされない FK 制約が存在する可能性があります。 運用データを使用する場合は、既存のデータを移行するための手順を実行する必要があります。 このセクションでは、FK 制約違反の修正例を示します。 これらのコードをバックアップせずに変更しないでください。 前の「[データベースを削除して再作成する](#drop)」セクションを完了している場合は、これらのコードを変更しないでください。

*{timestamp}_ComplexDataModel.cs* ファイルには次のコードが含まれています。

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

上のコードでは、null 非許容の `DepartmentID` FK が `Course` テーブルに追加されます。 前のチュートリアルのデータベースには `Course` の行が含まれるため、テーブルを移行して更新することはできません。

既存のデータを使用して `ComplexDataModel` の移行を実行するには、次のようにします。

* コードを変更して、新しい列 (`DepartmentID`) に既定値を設定します。
* "Temp" という名前の偽の学科を作成し、既定の学科として機能するようにします。

#### <a name="fix-the-foreign-key-constraints"></a>外部キー制約を修正する

`ComplexDataModel` 移行クラスで、`Up` メソッドを次のように更新します。

* *{timestamp}_ComplexDataModel.cs* ファイルを開きます。
* `DepartmentID` 列を `Course` テーブルに追加するコードの行をコメントアウトします。

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

次の強調表示されたコードを追加します。 新しいコードが `.CreateTable( name: "Department"` ブロックの後に配置されます。

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

前述の変更に伴い、既存の `Course` 行が、`ComplexDataModel.Up` メソッドの実行後に "Temp" 学科に関連付けられます。

ここで示す状況の処理方法は、このチュートリアルのために簡略化されています。 運用アプリは次のことを行います。

* コードまたはスクリプトを組み込み、`Department` 行と関連する `Course` 行を新しい `Department` 行に追加します。
* `Course.DepartmentID` の既定値や "Temp" 学科は使用しません。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。

  ```powershell
  Update-Database
  ```

`DbInitializer.Initialize` メソッドは空のデータベースでのみ動作するように設計されているので、Student テーブルと Course テーブルのすべての行を削除するには SSOX を使用します。 (連鎖削除によって Enrollment テーブルが処理されます。)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Visual Studio Code で SQL Server LocalDB を使用している場合は、次のコマンドを実行します。

  ```dotnetcli
  dotnet ef database update
  ```

---

アプリを実行します。 アプリを実行すると `DbInitializer.Initialize` メソッドが実行されます。 `DbInitializer.Initialize` では、新しいデータベースが設定されます。

## <a name="next-steps"></a>次の手順

次の 2 つのチュートリアルでは、関連データを読み取って更新する方法について説明します。

> [!div class="step-by-step"]
> [前のチュートリアル](xref:data/ef-rp/migrations)
> [次のチュートリアル](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

前のチュートリアルでは、3 つのエンティティで構成された基本的なデータ モデルを使用して作業を行いました。 このチュートリアルでは、次の作業を行います。

* エンティティとリレーションシップをさらに追加する。
* 書式設定、検証、データベース マッピングの規則を指定して、データ モデルをカスタマイズする。

完成したデータ モデルのエンティティ クラスは、次の図のようになります。

![エンティティ図](complex-data-model/_static/diagram.png)

解決できない問題が発生した場合は、[完成したアプリ](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)をダウンロードしてください。

## <a name="customize-the-data-model-with-attributes"></a>属性を使用してデータ モデルをカスタマイズする

このセクションでは、属性を使用してデータ モデルをカスタマイズします。

### <a name="the-datatype-attribute"></a>DataType 属性

学生のページには現在、登録日の時刻が表示されています。 通常、日付フィールドには日付のみが表示され、時刻は表示されません。

以下の強調表示されているコードを使用して、*Models/Student.cs* を更新します。

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 属性では、データベースの組み込み型よりも具体的なデータ型を指定します。 ここでは、日付と時刻ではなく、日付のみを表示する必要があります。 [DataType 列挙型](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くのデータ型のために用意されています。また、`DataType` 属性を使用して、アプリで型固有の機能を自動的に提供することもできます。 次に例を示します。

* `mailto:` リンクは `DataType.EmailAddress` に対して自動的に作成されます。
* ほとんどのブラウザーでは、`DataType.Date` に日付セレクターが提供されます。

`DataType` 属性は、HTML 5 ブラウザーが使用する HTML 5 `data-` (データ ダッシュと読む) 属性を出力します。 `DataType` 属性では検証は提供されません。

`DataType.Date` は、表示される日付の書式を指定しません。 既定で、日付フィールドはサーバーの [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) に基づき、既定の書式に従って表示されます。

`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` 設定では、書式設定を編集 UI にも適用する必要があることを指定します。 一部のフィールドでは `ApplyFormatInEditMode` を使用できません。 たとえば、通貨記号は一般的に編集テキスト ボックスには表示できません。

`DisplayFormat` 属性は単独で使用できます。 一般的には、`DataType` 属性を `DisplayFormat` 属性と一緒に使用することをお勧めします。 `DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。 `DataType` 属性には、`DisplayFormat` では得られない以下のような利点があります。

* ブラウザーで HTML5 機能を有効にすることができます。 たとえば、カレンダー コントロール、ロケールに適した通貨記号、メール リンク、クライアント側の入力検証などを表示します。
* 既定では、ブラウザーで、ロケールに基づいて正しい書式を使用してデータがレンダリングされます。

詳細については、[\<入力> タグ ヘルパーに関するドキュメント](xref:mvc/views/working-with-forms#the-input-tag-helper)を参照してください。

アプリを実行します。 Students インデックス ページに移動します。 時刻は表示されなくなりました。 `Student` モデルを使用するすべてのビューに、時刻なしの日付が表示されます。

![時刻なしの日付が表示されている Students インデックス ページ](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength 属性

データ検証規則と検証エラー メッセージは、属性を使用して指定できます。 [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 属性では、データ フィールドで使用できる最小文字長と最大文字長を指定します。 また、`StringLength` 属性ではクライアント側とサーバー側の検証も提供されます。 最小値は、データベース スキーマに影響しません。

`Student` モデルを次のコードで更新します。

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

上のコードでは、名前で使用可能な文字数を 50 に制限します。 `StringLength` 属性では、ユーザーが名前に空白を入力しないようにすることはできません。 [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 属性は、入力に制限を適用するために使用されます。 たとえば、次のコードでは、最初の文字を大文字にし、残りの文字をアルファベット順にすることを要求します。

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

次のようにアプリを実行します。

* Students のページに移動します。
* **[新規作成]** を選択し、50 文字を超える名前を入力します。
* **[作成]** を選択すると、クライアント側の検証でエラー メッセージが表示されます。

![文字列長エラーが表示されている Students インデックス ページ](complex-data-model/_static/string-length-errors.png)

**SQL Server オブジェクト エクスプローラー** (SSOX) で、**Student** テーブルをダブルクリックして、Student テーブル デザイナーを開きます。

![移行前の SSOX の Students テーブル](complex-data-model/_static/ssox-before-migration.png)

上の図には `Student` テーブルのスキーマが表示されています。 DB では移行が実行されていないため、名前フィールドに `nvarchar(MAX)` 型があります。 このチュートリアルの後半で移行が実行されたときに、名前フィールドが `nvarchar(50)` になります。

### <a name="the-column-attribute"></a>Column 属性

属性で、データベースへのクラスとプロパティのマッピング方法を制御することができます。 このセクションでは、`Column` 属性を使用して、`FirstMidName` プロパティの名前を DB の "FirstName" にマッピングします。

DB が作成されたときに、列名でモデルのプロパティ名が使用されます (`Column` 属性が使用されている場合を除く)。

`Student` モデルでは名フィールドに対して `FirstMidName` が使用されます。これは、フィールドにミドル ネームも含まれている場合があるためです。

以下の強調表示されているコードを使用して、*Student.cs* ファイルを更新します。

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

前述の変更に伴い、アプリの `Student.FirstMidName` は `Student` テーブルの `FirstName` 列にマップされます。

`Column` 属性を追加すると、`SchoolContext` をサポートするモデルが変更されます。 `SchoolContext` をサポートするモデルはデータベースと一致しなくなります。 移行を適用する前にアプリを実行した場合は、次の例外が生成されます。

```
SqlException: Invalid column name 'FirstName'.
```

DB を更新するには、次のようにします。

* プロジェクトをビルドします。
* プロジェクト フォルダーでコマンド ウィンドウを開きます。 以下のコマンドを入力し、新しい移行を作成して DB を更新します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

`migrations add ColumnFirstName` コマンドでは、以下の警告メッセージが生成されます。

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

名前フィールドは現在、50 文字に制限されているため、警告が生成されます。 DB の名前が 50 文字を超えた場合、51 番目から最後までの文字が失われます。

* アプリをテストします。

SSOX で Student テーブルを開きます。

![移行後の SSOX の Students テーブル](complex-data-model/_static/ssox-after-migration.png)

移行が適用される前の名前列の型は [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) でした。 現在の名前列は `nvarchar(50)` です。 列名は `FirstMidName` から `FirstName` に変わりました。

> [!Note]
> 次のセクションでは、いくつかのステージでアプリをビルドします。その場合、コンパイラ エラーが生成されます。 手順では、アプリをビルドするタイミングを指定します。

## <a name="student-entity-update"></a>Student エンティティの更新

![Student エンティティ](complex-data-model/_static/student-entity.png)

以下のコードを使用して、*Models/Student.cs* を更新します。

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Required 属性

`Required` 属性では、名前プロパティの必須フィールドを作成します。 値の型 (`DateTime`、`int`、`double` など) などの null 非許容型では `Required` 属性は必要ありません。 null にできない型は自動的に必須フィールドとして扱われます。

`Required` 属性は、`StringLength` 属性の最小長パラメーターに置き換えることができます。

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Display 属性

`Display` 属性では、テキスト ボックスのキャプションを "First Name"、"Last Name"、"Full Name"、"Enrollment Date" にするよう指定します。 既定のキャプションには、"Lastname" のように、単語を区切るスペースはありません。

### <a name="the-fullname-calculated-property"></a>FullName 集計プロパティ

`FullName` は集計プロパティであり、2 つの別のプロパティを連結して作成される値を返します。 `FullName` を設定することはできません。get アクセサーのみが含まれます。 データベースには `FullName` 列は作成されません。

## <a name="create-the-instructor-entity"></a>Instructor エンティティを作成する

![Instructor エンティティ](complex-data-model/_static/instructor-entity.png)

以下のコードを使用して、*Models/Instructor.cs* を作成します。

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

複数の属性を 1 行に配置することができます。 `HireDate` 属性は次のように記述できます。

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>CourseAssignments と OfficeAssignment ナビゲーション プロパティ

`CourseAssignments` と `OfficeAssignment` プロパティはナビゲーション プロパティです。

講師は任意の数のコースを担当できるため、`CourseAssignments` はコレクションとして定義されます。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

ナビゲーション プロパティに複数のエンティティが保持されている場合:

* エンティティを追加、削除、更新できるリスト型である必要があります。

ナビゲーション プロパティの型には次のようなものがあります。

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

`ICollection<T>` が指定されている場合は、EF Core で `HashSet<T>` コレクションが既定で作成されます。

`CourseAssignment` エンティティについては、多対多リレーションシップのセクションで説明します。

Contoso University のビジネス ルールには、講師は 1 つのオフィスのみを持つことができると示されています。 `OfficeAssignment` プロパティでは単一の `OfficeAssignment` エンティティが保持されます。 オフィスが割り当てられていない場合、`OfficeAssignment` は null です。

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>OfficeAssignment エンティティを作成する

![OfficeAssignment エンティティ](complex-data-model/_static/officeassignment-entity.png)

以下のコードを使用して、*Models/OfficeAssignment.cs* を作成します。

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Key 属性

`[Key]` 属性は、プロパティ名が classnameID や ID 以外である場合に、主キー (PK) としてプロパティを識別するために使用されます。

`Instructor` エンティティと `OfficeAssignment` エンティティの間には一対ゼロまたは一対一のリレーションシップがあります。 オフィスが割り当てられている講師についてのみ、オフィス割り当てが存在します。 `OfficeAssignment` PK は、`Instructor` エンティティに対する外部キー (FK) でもあります。 EF Core では、以下の理由で、`OfficeAssignment` の PK として `InstructorID` を自動的に認識することはできません。

* `InstructorID` は、ID や classnameID の名前付け規則には従っていません。

したがって、`Key` 属性は PK として `InstructorID` を識別するために使用されます。

```csharp
[Key]
public int InstructorID { get; set; }
```

列は依存リレーションシップに対するものであるため、既定では EF Core はキーを非データベース生成として扱います。

### <a name="the-instructor-navigation-property"></a>Instructor ナビゲーション プロパティ

`Instructor` エンティティの `OfficeAssignment` ナビゲーション プロパティは、以下の理由で null 許容型となります。

* 参照型 (クラスが null 許容型である)。
* 講師にオフィスが割り当てられていない可能性がある。

`OfficeAssignment` エンティティには、以下の理由で null 非許容の `Instructor` ナビゲーション プロパティがあります。

* `InstructorID` は null 非許容です。
* オフィス割り当ては講師なしでは存在できません。

`Instructor` エンティティに関連する `OfficeAssignment` エンティティがある場合、各エンティティにはそのナビゲーション プロパティの別のエンティティへの参照があります。

`[Required]` 属性を `Instructor` ナビゲーション プロパティに適用することができます。

```csharp
[Required]
public Instructor Instructor { get; set; }
```

上のコードは、関連する講師が存在する必要があることを指定します。 `InstructorID` 外部キー (PK でもある) が null 非許容であるため、上のコードは必要ありません。

## <a name="modify-the-course-entity"></a>Course エンティティを変更する

![Course エンティティ](complex-data-model/_static/course-entity.png)

以下のコードを使用して、*Models/Course.cs* を更新します。

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

`Course` エンティティには外部キー (FK) プロパティ `DepartmentID` があります。 `DepartmentID` は関連する `Department` エンティティを指します。 `Course` エンティティには `Department` ナビゲーション プロパティがあります。

EF Core では、モデルに関連エンティティのナビゲーション プロパティがある場合、データ モデルの FK プロパティは必要ありません。

EF Core は、必要に応じて、データベースで自動的に FK を作成します。 EF Core は、自動的に作成された FK に対して、[シャドウ プロパティ](/ef/core/modeling/shadow-properties)を作成します。 データ モデルに FK がある場合は、更新をより簡単かつ効率的に行うことができます。 たとえば、FK プロパティ `DepartmentID` が含まれて*いない* モデルがあるとします。 Course エンティティが編集用にフェッチされた場合は、次のようになります。

* `Department` エンティティは、明示的に読み込まれない場合、null となります。
* Course エンティティを更新するには、`Department` エンティティを最初にフェッチする必要があります。

FK エンティティ `DepartmentID` がデータ モデルに含まれている場合は、更新前に `Department` エンティティをフェッチする必要はありません。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 属性

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 属性では、PK をデータベースで生成するのではなく、アプリケーションで提供するように指定します。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

既定では、EF Core は、PK 値が DB で生成されることを前提とします。 通常は、PK 値を DB で生成するのが最適です。 `Course` エンティティの場合、PK はユーザーが指定します。 たとえば、数学科の場合は 1000 シリーズ、英文科の場合は 2000 シリーズなどのコース番号となります。

`DatabaseGenerated` 属性は、既定値を生成する場合にも使用できます。 たとえば、DB では、行が作成または更新された日付を記録するための日付フィールドを自動的に生成できます。 詳細については、「[生成される値](/ef/core/modeling/generated-properties)」を参照してください。

### <a name="foreign-key-and-navigation-properties"></a>外部キー プロパティとナビゲーション プロパティ

`Course` エンティティの外部キー (FK) プロパティとナビゲーション プロパティには、以下のリレーションシップが反映されます。

コースが 1 つの学科に割り当てられています。したがって、`DepartmentID` FK と `Department` ナビゲーション プロパティがあります。

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

コースには任意の数の学生が登録できるため、`Enrollments` ナビゲーション プロパティはコレクションとなります。

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

コースは複数の講師が担当する場合があるため、`CourseAssignments` ナビゲーション プロパティはコレクションとなります。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` については、[後で](#many-to-many-relationships)説明します。

## <a name="create-the-department-entity"></a>Department エンティティを作成する

![Department エンティティ](complex-data-model/_static/department-entity.png)

以下のコードを使用して、*Models/Department.cs* を作成します。

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Column 属性

これまでは、`Column` 属性が列名のマッピングを変更するために使用されました。 `Department` エンティティのコードでは、`Column` 属性は SQL データ型のマッピングを変更するために使用されます。 `Budget` 列は、次のように、DB の SQL Server の money 型を使用して定義されます。

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

通常、列マッピングは必要ありません。 通常、EF Core はプロパティの CLR 型に基づいて、適切な SQL Server のデータ型を選択します。 CLR `decimal` 型は SQL Server の `decimal` 型にマップされます。 `Budget` は通貨用であり、通貨には money データ型がより適しています。

### <a name="foreign-key-and-navigation-properties"></a>外部キー プロパティとナビゲーション プロパティ

FK およびナビゲーション プロパティには、次のリレーションシップが反映されます。

* 学科には管理者が存在する場合とそうでない場合があります。
* 管理者は常に講師です。 したがって、`InstructorID` プロパティは `Instructor` エンティティに対する FK として含まれます。

ナビゲーション プロパティは `Administrator` という名前ですが、`Instructor` エンティティを保持します。

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

上のコードの疑問符 (?) は、プロパティが null 許容であることを示します。

学科には複数のコースがある場合があるため、Courses ナビゲーション プロパティがあります。

```csharp
public ICollection<Course> Courses { get; set; }
```

メモ:規則により、EF Core では null 非許容の FK と多対多リレーションシップに対して連鎖削除が有効になります。 連鎖削除では、循環連鎖削除規則が適用される可能性があります。 循環連鎖削除規則が適用されると、移行の追加時に例外が発生します。

たとえば、`Department.InstructorID` プロパティが null 許容として定義されなかった場合、次のようになります。

* EF Core は、講師が削除されたときに学科を削除するように連鎖削除規則を構成します。
* 講師が削除されたときに学科を削除するのは、意図した動作ではありません。
* 次の [fluent API](#fluent-api-alternative-to-attributes) で、連鎖の代わりに制限規則を設定します。

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

上のコードでは、学科と講師のリレーションシップの連鎖削除が無効になります。

## <a name="update-the-enrollment-entity"></a>Enrollment エンティティを更新する

1 件の登録レコードは、1 人の学生が受講する 1 つのコースに対するものです。

![Enrollment エンティティ](complex-data-model/_static/enrollment-entity.png)

以下のコードを使用して、*Models/Enrollment.cs* を更新します。

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>外部キー プロパティとナビゲーション プロパティ

FK プロパティとナビゲーション プロパティには、次のリレーションシップが反映されます。

登録レコードは 1 つのコースに対するものであるため、`CourseID` FK プロパティと `Course` ナビゲーション プロパティがあります。

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

登録レコードは 1 人の学生に対するものであるため、`StudentID` FK プロパティと `Student` ナビゲーション プロパティがあります。

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>多対多リレーションシップ

`Student` エンティティと `Course` エンティティの間には多対多リレーションシップがあります。 `Enrollment` エンティティは、データベースで*ペイロードがある*多対多結合テーブルとして機能します。 "ペイロードがある" とは、`Enrollment` テーブルに、結合テーブルの FK 以外に追加データが含まれていることを意味します (ここでは PK と `Grade`)。

次の図は、エンティティ図でこれらのリレーションシップがどのようになるかを示しています (この図は、EF 6.x 用の [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) を使用して生成されたものです。 このチュートリアルでは図は作成しません)。

![Student と Course の多対多リレーションシップ](complex-data-model/_static/student-course.png)

各リレーションシップ線の一方の端に 1 が、もう一方の端にアスタリスク (*) があり、1 対多リレーションシップであることを示しています。

`Enrollment` テーブルに成績情報が含まれていない場合、含める必要があるのは 2 つの FK (`CourseID` と `StudentID`) のみです。 ペイロードがない多対多結合テーブルは純粋結合テーブル (PJT) と呼ばれる場合があります。

`Instructor` および `Course` エンティティには、純粋結合テーブルを使用する多対多リレーションシップがあります。

メモ:EF 6.x では多対多リレーションシップの暗黙の結合テーブルがサポートされますが、EF Core ではサポートされません。 詳細については、「[Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)」 (EF Core 2.0 の多対多リレーションシップ) を参照してください。

## <a name="the-courseassignment-entity"></a>CourseAssignment エンティティ

![CourseAssignment エンティティ](complex-data-model/_static/courseassignment-entity.png)

以下のコードを使用して、*Models/CourseAssignment.cs* を作成します。

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>講師対コース

![講師対コース m:M](complex-data-model/_static/courseassignment.png)

講師対コースの多対多リレーションシップは、

* エンティティ セットで表される必要がある結合テーブルが必要です。
* 純粋結合テーブル (ペイロードがないテーブル) です。

結合エンティティには `EntityName1EntityName2` という名前が付けるのが一般的です。 たとえば、このパターンを使用する講師対コースの結合テーブルは `CourseInstructor` となります。 ただし、リレーションシップを説明する名前を使用することをお勧めします。

データ モデルは始めは単純なものであっても大きくなります。 ペイロードがない結合 (PJT) は頻繁に進化し、ペイロードが含まれるようになります。 最初にわかりやすいエンティティ名を付けておけば、結合テーブルが変更されたときに名前を変更する必要はありません。 結合エンティティでは、ビジネス ドメインに独自の自然な (場合によっては 1 単語の) 名前を指定することが理想的です。 たとえば、Books と Customers は Ratings という結合エンティティでリンクできます。 講師対コースの多対多リレーションシップの場合、`CourseAssignment` は `CourseInstructor` より優先されます。

### <a name="composite-key"></a>複合キー

FK は null 非許容です。 `CourseAssignment` の 2 つの FK (`InstructorID` と `CourseID`) を組み合わせて使用し、`CourseAssignment` テーブルの各行を一意に識別します。 `CourseAssignment` には専用の PK は必要ありません。 `InstructorID` および `CourseID` プロパティは複合 PK として機能します。 EF Core に複合 PK を指定する唯一の方法は、*fluent API* を使用することです。 次のセクションでは、複合 PK の構成方法を示します。

複合キーでは、必ず次のようになります。

* 1 つのコースに対して複数の行が許可される。
* 1 人の講師に対して複数の行が許可される。
* 同じ講師とコースに対して複数の行が許可されない。

`Enrollment` 結合エンティティでは独自の PK を定義するため、このような重複が考えられます。 このような重複を防ぐには、次のようにします。

* FK フィールドに一意のインデックスを追加する。または
* `CourseAssignment` と同様の複合主キーを使用して、`Enrollment` を構成する。 詳細については、「[インデックス](/ef/core/modeling/indexes)」を参照してください。

## <a name="update-the-db-context"></a>DB コンテキストを更新する

次の強調表示されているコードを *Data/SchoolContext.cs* に追加します。

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

上のコードでは新しいエンティティが追加され、`CourseAssignment` エンティティの複合 PK が構成されます。

## <a name="fluent-api-alternative-to-attributes"></a>属性の代わりに fluent API を使用する

上のコードの `OnModelCreating` メソッドでは、*fluent API* を使用して EF Core の動作を構成します。 API は "fluent" と呼ばれます。これは、多くの場合、一連のメソッド呼び出しを単一のステートメントにまとめて使用されるためです。 [次のコード](/ef/core/modeling/#use-fluent-api-to-configure-a-model)は fluent API の例です。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

このチュートリアルでは、属性で実行できない DB マッピングの場合にのみ、fluent API を使用します。 ただし、fluent API では、属性で実行できる書式設定、検証、マッピング規則のほとんどを指定できます。

`MinimumLength` などの一部の属性は fluent API で適用できません。 `MinimumLength` ではスキーマを変更せず、最小長の検証規則のみを適用します。

一部の開発者は fluent API のみを使用することを選ぶため、エンティティ クラスを "クリーン" な状態に保つことができます。 属性と fluent API を混在させることができます。 (複合 PK を指定して) fluent API でのみ実行できる構成がいくつかあります。 属性 (`MinimumLength`) でのみ実行できる構成もいくつかあります。 次のように、fluent API または属性を使用することをお勧めします。

* これら 2 つの方法のいずれかを選択する。
* できるだけ一貫性を保つために選択した方法を使用する。

このチュートリアルで使用する属性のいくつかは、次の用途に使用されます。

* 検証のみ (`MinimumLength` など)。
* EF Core 構成のみ (`HasKey` など)。
* 検証と EF Core の構成 (`[StringLength(50)]` など)。

属性と fluent API の詳細については、「[構成の方法](/ef/core/modeling/)」を参照してください。

## <a name="entity-diagram-showing-relationships"></a>リレーションシップを示すエンティティ図

次の図では、完成した School モデルに対して EF Power Tools で作成される図を示します。

![エンティティ図](complex-data-model/_static/diagram.png)

上の図には以下が示されています。

* いくつかの一対多リレーションシップの線 (1 対 \*)。
* `Instructor` エンティティと `OfficeAssignment` エンティティの間の一対ゼロまたは一対一リレーションシップの線 (1 対 0..1)。
* `Instructor` エンティティと `Department` エンティティの間のゼロ対一またはゼロ対多リレーションシップの線 (1 対 0..1)。

## <a name="seed-the-db-with-test-data"></a>テスト データで DB をシードする

*Data/DbInitializer.cs* のコードを更新します。

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

上のコードでは、新しいエンティティのシード データが提供されます。 このコードのほとんどで新しいエンティティ オブジェクトが作成され、サンプル データが読み込まれます。 サンプル データはテストに使用されます。 多対多結合テーブルがシードされる方法の例については、`Enrollments` と `CourseAssignments` を参照してください。

## <a name="add-a-migration"></a>移行を追加する

プロジェクトをビルドします。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

上のコマンドは、考えられるデータ損失に関する警告を表示します。

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

`database update` コマンドを実行すると、次のエラーが生成されます。

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>移行を適用する

既存のデータベースができたので、将来の変更を適用する方法について検討する必要があります。 このチュートリアルでは、2 つの方法を示します。

* [データベースを削除して再作成する](#drop)
* [移行を既存のデータベースに適用する](#applyexisting)。 この方法はより複雑で時間がかかりますが、実際の運用環境では推奨される方法です。 **注**:これは、チュートリアルのオプションのセクションです。 削除と再作成の手順を行い、このセクションはスキップしてもかまいません。 このセクションの手順に従う場合は、削除と再作成の手順を行わないでください。 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>データベースを削除して再作成する

更新された `DbInitializer` のコードでは、新しいエンティティのシード データを追加します。 EF Core に新しい DB を強制的に作成させるには、DB を削除して更新します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。

```powershell
Drop-Database
Update-Database
```

PMC から `Get-Help about_EntityFrameworkCore` を実行してヘルプ情報を入手します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。 プロジェクト フォルダーには *Startup.cs* ファイルが含まれます。

コマンド ウィンドウで次のコマンドを入力します。

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

アプリを実行します。 アプリを実行すると `DbInitializer.Initialize` メソッドが実行されます。 `DbInitializer.Initialize` は新しい DB を設定します。

SSOX で DB を開きます。

* SSOX が既に開いている場合は、 **[更新]** ボタンをクリックします。
* **[Tables]\(テーブル\)** ノードを展開します。 作成されたテーブルが表示されます。

![SSOX のテーブル](complex-data-model/_static/ssox-tables.png)

**CourseAssignment** テーブルを確認します。

* **CourseAssignment** テーブルを右クリックして、 **[データの表示]** を選択します。
* **CourseAssignment** テーブルにデータが含まれていることを確認します。

![SSOX の CourseAssignment データ](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>移行を既存のデータベースに適用する

このセクションは省略可能です。 以下の手順は、前の「[データベースを削除して再作成する](#drop)」セクションをスキップした場合にのみ使用できます。

既存のデータで移行が実行されている場合、既存のデータでは満たされない FK 制約が存在する可能性があります。 運用データを使用する場合は、既存のデータを移行するための手順を実行する必要があります。 このセクションでは、FK 制約違反の修正例を示します。 これらのコードをバックアップせずに変更しないでください。 前のセクションを完了し、データベースを更新した場合は、これらのコードを変更しないでください。

*{timestamp}_ComplexDataModel.cs* ファイルには次のコードが含まれています。

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

上のコードでは、null 非許容の `DepartmentID` FK が `Course` テーブルに追加されます。 前のチュートリアルの DB には `Course` の行が含まれるため、テーブルを移行して更新することはできません。

既存のデータを使用して `ComplexDataModel` の移行を実行するには、次のようにします。

* コードを変更して、新しい列 (`DepartmentID`) に既定値を設定します。
* "Temp" という名前の偽の学科を作成し、既定の学科として機能するようにします。

#### <a name="fix-the-foreign-key-constraints"></a>外部キー制約を修正する

`ComplexDataModel` クラスの `Up` メソッドを更新します。

* *{timestamp}_ComplexDataModel.cs* ファイルを開きます。
* `DepartmentID` 列を `Course` テーブルに追加するコードの行をコメントアウトします。

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

次の強調表示されたコードを追加します。 新しいコードが `.CreateTable( name: "Department"` ブロックの後に配置されます。

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

前述の変更に伴い、既存の `Course` 行が、`ComplexDataModel` `Up` メソッドの実行後に "Temp" 学科に関連付けられます。

運用アプリは次のことを行います。

* コードまたはスクリプトを組み込み、`Department` 行と関連する `Course` 行を新しい `Department` 行に追加します。
* `Course.DepartmentID` の既定値や "Temp" 学科は使用しません。

次のチュートリアルでは関連するデータについて説明します。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン (パート 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [このチュートリアルの YouTube バージョン (パート 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [前へ](xref:data/ef-rp/migrations)
> [次へ](xref:data/ef-rp/read-related-data)

::: moniker-end
