---
title: ASP.NET Core の Razor ページと EF Core - 関連データの読み込み - 6/8
author: tdykstra
description: このチュートリアルでは、関連データ (Entity Framework がナビゲーション プロパティに読み込むデータ) の読み取りと表示を行います。
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 02b52dee0b661ad26cd6fa9fea08fcea3d7dd9bd
ms.sourcegitcommit: f62014bb558ff6f8fdaef2e96cb05986e216aacd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2019
ms.locfileid: "71592299"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a>ASP.NET Core の Razor ページと EF Core - 関連データの読み込み - 6/8

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

このチュートリアルでは、関連データを読み取って表示する方法を示します。 関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。

以下の図は、このチュートリアルの完成したページを示しています。

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>一括読み込み、明示的読み込み、遅延読み込み

EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。

* [一括読み込み](/ef/core/querying/related-data#eager-loading)。 一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。 エンティティが読み取られるときに、その関連データが取得されます。 これは通常、必要なデータをすべて取得する 1 つの結合クエリになります。 EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。 複数のクエリを発行することは、1 つの巨大なクエリよりも効率的である可能性があります。 一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。

  * メイン クエリに 1 つのクエリ 
  * 読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ

* `Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。 "修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。 `Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。 ナビゲーション プロパティのデータが明示的に含まれ*ない*場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。

* [明示的読み込み](/ef/core/querying/related-data#explicit-loading)。 エンティティが最初に読み込まれるときに、関連データは取得されません。 必要なときに関連するデータを取得するコードを記述する必要があります。 分離したクエリによる明示的読み込みにより、複数のクエリがデータベースに送信されます。 明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。 明示的読み込みを行うには、`Load` メソッドを使用します。 次に例を示します。

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* [遅延読み込み](/ef/core/querying/related-data#lazy-loading)。 [遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。 エンティティが最初に読み込まれるときに、関連データは取得されません。 ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。 初めてナビゲーション プロパティにアクセスされるたびに、クエリがデータベースに送信されます。

## <a name="create-course-pages"></a>Course ページの作成

`Course` エンティティには、関連する `Department` エンティティを含むナビゲーション プロパティが含まれています。

![Course.Department](read-related-data/_static/dep-crs.png)

コースに割り当てられている部署の名前を表示するには

* 関連する `Department` エンティティを `Course.Department` ナビゲーション プロパティに読み込みます。
* `Department` エンティティの `Name` プロパティから名前を取得します。

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Course ページのスキャフォールディング

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。

  * *Pages/Courses* フォルダーを作成します。
  * モデル クラスに `Course` を使用します。
  * 新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Pages/Courses* フォルダーを作成します。

* 次のコマンドを実行して、Course モデルをスキャフォールディングします。

  **Windows の場合:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **Linux または macOS の場合**:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* *Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。 スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。 `Include` メソッドが一括読み込みを指定します。

* アプリを実行し、 **[Courses]** リンクを選択します。 Department 列に `DepartmentID` が表示されますが、これには役に立ちません。

### <a name="display-the-department-name"></a>部署名の表示

次のコードを使用して Pages/Courses/Index.cshtml.cs を更新します。

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

上記のコードでは、`Course` プロパティを `Courses` に変更し、`AsNoTracking` を追加します。 `AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。 エンティティは現在のコンテキストでは更新されないため、追跡する必要はありません。

次のコードを使用して *Pages/Courses/Index.cshtml* を更新します。

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

スキャフォールディング コードに、次の変更が行われました。

* `Course` プロパティ名は `Courses` に変更されました。
* `CourseID` プロパティ値を示す **Number** 列が追加されました。 既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。 ただし、このケースでは、主キーは意味があります。
* 部門名が表示されるように、**Department** 列を変更しました。 コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Select を使用した関連データの読み込み

`OnGetAsync` メソッドでは、`Include` メソッドを使用して関連データを読み込みます。 `Select` メソッドは、必要な関連データだけを読み込む代替手段です。 `Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。 コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。

次のコードは、`Select` メソッドを使用して関連データを読み込みます。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

`CourseViewModel`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

完全な例については、[IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。

## <a name="create-instructor-pages"></a>Instructor ページの作成

このセクションでは、Instructor ページをスキャフォールディングし、関連する Courses と Enrollments を Instructors Index ページに追加します。

<a name="IP"></a>
![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

このページは、次の方法で関連データを読み取って表示します。

* インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。 `Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。 `OfficeAssignment` エンティティには一括読み込みが使用されています。 一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。 この場合、インストラクターへのオフィスの割り当てが表示されます。
* ユーザーがインストラクターを選択すると、関連する `Course` エンティティが表示されます。 `Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。 `Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。 このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。 この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。
* ユーザーがコースを選択すると、`Enrollments` エンティティからの関連データが表示されます。 上の図では、受講者名とグレードが表示されています。 `Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。

### <a name="create-a-view-model"></a>ビュー モデルを作成する

Instructors ページには、3 つの異なるテーブルからのデータが表示されます。 3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが必要です。

次のコードを使用して、*SchoolViewModels/InstructorIndexData.cs* を作成します。

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Instructor ページのスキャフォールディング

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。

  * *Pages/Instructors* フォルダーを作成します。
  * モデル クラスに `Instructor` を使用します。
  * 新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Pages/Instructors* フォルダーを作成します。

* 次のコマンドを実行して、Instructor モデルをスキャフォールディングします。

  **Windows の場合:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **Linux または macOS の場合**:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

更新する前にスキャフォールディング ページがどのように表示されるかを確認するには、アプリを実行し、Instructors ページに移動します。

次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。

*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

このコードでは、次のナビゲーション プロパティに一括読み込みを指定します。

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

`CourseAssignments` と `Course` に対する `Include` メソッドと `ThenInclude` メソッドの繰り返しに注意してください。 この繰り返しは、`Course` エンティティの 2 つのナビゲーション プロパティに一括読み込みを指定するために必要です。

次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。 ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。

`Where` メソッドはコレクションを返します。 しかし、この場合は、フィルターによって 1 つのエンティティが選択されます。 そのため、`Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換するために呼び出されます。 `Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。 `CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。 コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。 代わりに、コレクションが空の場合に既定値 (この場合は null) を返す `SingleOrDefault` を使用します。

次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Instructors/Index ページを更新する

次のコードを使用して *Pages/Instructors/Index.cshtml* を更新します。

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

上記のコードは、次の変更を加えます。

* `page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。 `"{id:int?}"` はルート テンプレートです。 ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。 たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。

  `https://localhost:5001/Instructors?id=2`

  ページ ディレクティブが `@page "{id:int?}"` の場合、URL は次のようになります。

  `https://localhost:5001/Instructors/2`

* `item.OfficeAssignment` が null ではない場合にのみ、`item.OfficeAssignment.Location` を表示する **Office** 列を追加します。 これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* インストラクターごとに担当したコースを表示する **Courses** 列を追加します。 この Razor 構文の詳細については、「[明示的な行の遷移](xref:mvc/views/razor#explicit-line-transition)」を参照してください。

* 選択したインストラクターとコースの `tr` 要素に `class="success"` を動的に追加するコードを追加します。 これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Select** とラベル付けされる新しいハイパーリンクを追加します。 このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* 選択した Instructor のコースのテーブルを追加します。

* 選択したコースの学生登録のテーブルを追加します。

アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。 `OfficeAssignment` が null の場合、空のテーブル セルが表示されます。

インストラクターの **Select** リンクをクリックします。 行スタイルの変更とそのインストラクターに割り当てられたコースが表示されます。

コースを選択して、登録済みの受講者とその成績のリストを表示します。

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Single を使用する

`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Where 条件を使った `Single` の使用は、個人の好みの問題です。 `Where` メソッドを使用しても利点はありません。

## <a name="explicit-loading"></a>明示的読み込み

現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

ユーザーがコースの登録を表示することはほとんどないとします。 その場合、最適化は要求された場合にのみ登録データを読み込むことです。 このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。

次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。 コースが選択されると、明示的読み込みコードで以下が取得されます。

* 選択したコースの `Enrollment` エンティティ。
* 各 `Enrollment` の `Student` エンティティ。

上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。 追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。

アプリをテストします。 ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。

## <a name="next-steps"></a>次の手順

次のチュートリアルでは、関連データの更新方法を示します。

>[!div class="step-by-step"]
>[前のチュートリアル](xref:data/ef-rp/complex-data-model)
>[次のチュートリアル](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このチュートリアルでは、関連データが読み取られ、表示されます。 関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。

解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。 [ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)

以下の図は、このチュートリアルの完成したページを示しています。

![Courses/Index ページ](read-related-data/_static/courses-index.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>関連データの一括読み込み、明示的読み込み、遅延読み込み

EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。

* [一括読み込み](/ef/core/querying/related-data#eager-loading)。 一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。 エンティティが読み取られるときに、その関連データが取得されます。 これは通常、必要なすべてのデータを取得する 1 つの結合クエリになります。 EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。 複数のクエリを発行することで、1 つのクエリしかなかった EF6 の一部のクエリよりも、効率を高めることができます。 一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。

  * メイン クエリに 1 つのクエリ 
  * 読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ

* `Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。 "修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。 `Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。 ナビゲーション プロパティのデータが明示的に含まれ*ない*場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。

* [明示的読み込み](/ef/core/querying/related-data#explicit-loading)。 エンティティが最初に読み込まれるときに、関連データは取得されません。 必要なときに関連するデータを取得するコードを記述する必要があります。 分離したクエリによる明示的読み込みにより、複数のクエリが DB に送信されます。 明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。 明示的読み込みを行うには、`Load` メソッドを使用します。 次に例を示します。

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* [遅延読み込み](/ef/core/querying/related-data#lazy-loading)。 [遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。 エンティティが最初に読み込まれるときに、関連データは取得されません。 ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。 初めてナビゲーション プロパティにアクセスされるたびに、クエリが DB に送信されます。

* `Select` 演算子は必要な関連データのみを読み込みます。

## <a name="create-a-course-page-that-displays-department-name"></a>部門名を表示する Course ページを作成する

Course エンティティには、`Department` エンティティを含むナビゲーション プロパティが含まれています。 `Department` エンティティには、コースが割り当てられる部門が含まれています。

コースの一覧で割り当てられている部門の名前を表示するには:

* `Department` エンティティから `Name` プロパティを取得します。
* `Department` エンティティは `Course.Department` ナビゲーション プロパティから取得されます。

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Course モデルのスキャフォールディング

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Course` を使用します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 次のコマンドを実行します。

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

上記のコマンドは、`Course` モデルをスキャフォールディングします。 Visual Studio でプロジェクトを開きます。

*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。 スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。 `Include` メソッドが一括読み込みを指定します。

アプリを実行し、 **[Courses]** リンクを選択します。 Department 列に `DepartmentID` が表示されますが、これには役に立ちません。

`OnGetAsync` メソッドを次のコードで更新します。

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

上のコードは `AsNoTracking` を追加します。 `AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。 これらのエンティティは現在のコンテキストでは更新されないため、追跡されません。

次の強調表示されているマークアップで *Pages/Courses/Index.cshtml* を更新します。

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

スキャフォールディング コードに、次の変更が行われました。

* 見出しが Index から Courses に変更されました。
* `CourseID` プロパティ値を示す **Number** 列が追加されました。 既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。 ただし、このケースでは、主キーは意味があります。
* 部門名が表示されるように、**Department** 列を変更しました。 コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。

![Courses/Index ページ](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Select を使用した関連データの読み込み

`OnGetAsync` メソッドは、`Include` メソッドを使用して関連データを読み込みます。

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select` 演算子は必要な関連データのみを読み込みます。 `Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。 コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。

次のコードは、`Select` メソッドを使用して関連データを読み込みます。

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

完全な例については、[IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>コース登録を示す Instructors ページを作成する

このセクションでは、Instructors ページが作成されます。

<a name="IP"></a>
![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

このページは、次の方法で関連データを読み取って表示します。

* インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。 `Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。 `OfficeAssignment` エンティティには一括読み込みが使用されています。 一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。 この場合、インストラクターへのオフィスの割り当てが表示されます。
* ユーザーがインストラクターを選択 (上の図では Harui) すると、関連 `Course` エンティティが表示されます。 `Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。 `Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。 このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。 この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。
* ユーザーがコースを選択すると (上の図では Chemistry (化学))、`Enrollments` エンティティからの関連データが表示されます。 上の図では、受講者名とグレードが表示されています。 `Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Instructor インデックス ビューのビュー モデルを作成する

Instructors ページには、3 つの異なるテーブルからのデータが表示されます。 3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが作成されます。

次のコードを使用して、*SchoolViewModels* フォルダー内に *InstructorIndexData.cs* を作成します。

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Instructor モデルのスキャフォールディング

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Instructor` を使用します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 次のコマンドを実行します。

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

上記のコマンドは、`Instructor` モデルをスキャフォールディングします。 
アプリを実行し、Instructors ページに移動します。

*Pages/Instructors/Index.cshtml.cs* を次のコードで置き換えます。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。

*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

クエリには次の 2 つが含まれています。

* `OfficeAssignment`:[Instructors ビュー](#IP)に表示されます。
* `CourseAssignments`:担当したコースを取り込みます。

### <a name="update-the-instructors-index-page"></a>Instructors/Index ページを更新する

次のマークアップを使用して *Pages/Instructors/Index.cshtml* を更新します。

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

上記のマークアップは、次の変更を加えます。

* `page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。 `"{id:int?}"` はルート テンプレートです。 ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。 たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。

  `http://localhost:1234/Instructors?id=2`

  ページ ディレクティブが `@page "{id:int?}"` の場合、上記の URL は次のようになります。

  `http://localhost:1234/Instructors/2`

* ページ タイトルは **Instructors** です。
* `item.OfficeAssignment` が null ではない場合にのみ `item.OfficeAssignment.Location` を表示する **Office** 列を追加しました。 これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* インストラクターごとに担当したコースを表示する **Courses** 列を追加しました。 この Razor 構文の詳細については、「[明示的な行の遷移](xref:mvc/views/razor#explicit-line-transition)」を参照してください。

* 選択したインストラクターの `tr` 要素に `class="success"` を動的に追加するコードを追加しました。 これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Select** とラベル付けされるハイパーリンクを追加しました。 このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。 OfficeAssignment` が null の場合、空のテーブル セルが表示されます。

**[Select]** リンクをクリックします。 行のスタイルが変更されます。

### <a name="add-courses-taught-by-selected-instructor"></a>選択したインストラクターが担当するコースを追加する

次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドを更新します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

`public int CourseID { get; set; }` を追加します

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

更新されたクエリを確認します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

上記のクエリは `Department` エンティティを追加します。

次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。 選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。 ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where` メソッドはコレクションを返します。 上記の `Where` メソッドでは、1 つの `Instructor` エンティティのみが返されます。 `Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換します。 `Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。 `CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。 コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。 代わりに、コレクションが空の場合に既定値を返す (この場合は null) `SingleOrDefault` を使用します。 空のコレクションで `SingleOrDefault` を使用すると、次のようになります。

* (null 参照で `Courses` プロパティを見つけようとすると) 例外になります。
* 例外メッセージに問題の原因が明確に示されない場合があります。

次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

*Pages/Instructors/Index.cshtml* Razor ページの末尾に次のマークアップを追加します。

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

上記のマークアップは、インストラクターが選択されたときに、インストラクターに関連するコースのリストを表示します。

アプリをテストします。 Instructors ページの **[Select]** リンクをクリックします。

### <a name="show-student-data"></a>受講者データを表示する

このセクションでは、選択したコースの受講者データを表示するため、アプリが更新されます。

次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドのクエリを更新します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

*Pages/Instructors/Index.cshtml* を更新します。 ファイルの末尾に次のマークアップを追加します。

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

上記のマークアップは、選択したコースに登録されている受講者のリストを表示します。

ページを更新し、インストラクターを選択します。 コースを選択して、登録済みの受講者とその成績のリストを表示します。

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Single を使用する

`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

上記の `Single` アプローチでは、`Where` を使用すること以上のメリットは提供されません。 一部の開発者は、`Single` アプローチ スタイルを選択します。

## <a name="explicit-loading"></a>明示的読み込み

現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

ユーザーがコースの登録を表示することはほとんどないとします。 その場合、最適化は要求された場合にのみ登録データを読み込むことです。 このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。

次のコードを使用して `OnGetAsync` を更新します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。 コースが選択されると、強調表示されたコードが以下を取得します。

* 選択したコースの `Enrollment` エンティティ。
* 各 `Enrollment` の `Student` エンティティ。

上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。 追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。

アプリをテストします。 ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。

次のチュートリアルでは、関連データの更新方法を示します。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン (part1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [このチュートリアルの YouTube バージョン (part2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[前へ](xref:data/ef-rp/complex-data-model)
>[次へ](xref:data/ef-rp/update-related-data)

::: moniker-end
