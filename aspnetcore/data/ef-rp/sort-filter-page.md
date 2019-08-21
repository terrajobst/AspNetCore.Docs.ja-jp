---
title: ASP.NET Core の Razor Pages と EF Core - 並べ替え、フィルター、ページング - 3/8
author: rick-anderson
description: このチュートリアルでは、ASP.NET Core および Entity Framework Core を使用して並べ替え、フィルター、ページング機能を Razor ページに追加します。
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 70f4220674502265963410b928b9340aa20e0cea
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914107"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>ASP.NET Core の Razor Pages と EF Core - 並べ替え、フィルター、ページング - 3/8

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

このチュートリアルでは、Students ページに並べ替え、フィルター、ページング機能を追加します。

次の図は、完成したページを示しています。 列見出しはクリックできるリンクとなっており、クリックすると列が並べ替えられます。 列見出しを繰り返しクリックすると、昇順と降順の並べ替え順序が切り替えられます。

![Students インデックス ページ](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>並べ替えの追加

*Pages/Students/Index.cshtml.cs* のコードを次のコードに置き換え、並べ替えを追加します。

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All&highlight=21-24,26,28-52)]

上のコードでは以下の操作が行われます。

* プロパティを追加して、並べ替えパラメーターを含めます。
* `Student` プロパティの名前を `Students` に変更します。
* `OnGetAsync` メソッドのコードを置き換えます。

`OnGetAsync` メソッドでは、URL 内のクエリ文字列から `sortOrder` パラメーターを受け取ります。 (クエリ文字列を含む) URL が[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。

`sortOrder` パラメーターは "Name" または "Date" です。 `sortOrder` パラメーターの後に必要に応じて "_desc" を続け、降順を指定します。 既定の並べ替え順序は昇順です。

インデックス ページが、**Students** リンクから要求された場合、クエリ文字列はありません。 学生は、姓の昇順で表示されます。 `switch` ステートメントでは姓の昇順が既定値 (フォールスルー ケース) です。 ユーザーが列見出しリンクをクリックすると、適切な `sortOrder` 値がクエリ文字列値で提供されます。

`NameSort` および `DateSort` は、Razor Page で、適切なクエリ文字列値を持つ列見出しのハイパーリンクを構成するために使用されます。

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

このコードでは、C# の条件演算子 [?:](/dotnet/csharp/language-reference/operators/conditional-operator) を使用します。 `?:` 演算子は三項演算子です (3 つのオペランドを受け取ります)。 最初の行は、`sortOrder` が null または空の場合に、`NameSort` を "name_desc" に設定することを指定します。 `sortOrder` が null または空**ではない**場合、`NameSort` は空の文字列に設定されます。

これらの 2 つのステートメントを使用して、次のようにページで列見出しのハイパーリンクを設定することができます。

| 既定の並べ替え順   | 姓のハイパーリンク | 日付のハイパーリンク |
|:--------------------:|:-------------------:|:--------------:|
| 姓の昇順  | descending          | ascending      |
| 姓の降順 | ascending           | ascending      |
| 日付の昇順       | ascending           | descending     |
| 日付の降順      | ascending           | ascending      |

このメソッドは、並べ替える列を指定するのに LINQ to Entities を使用します。 このコードは、switch ステートメントの前に `IQueryable<Student>` を初期化し、switch ステートメントでそれを変更します。

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

`IQueryable` が作成または変更されるときには、クエリは、データベースに送信されません。 クエリは、`IQueryable` オブジェクトがコレクションに変換されるまで実行されません。 `IQueryable` は、`ToListAsync` などのメソッドを呼び出すことで、コレクションに変換されます。 そのため、`IQueryable` コードの結果として、次のステートメントまで実行されない 1 つのクエリになります。

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

並べ替え可能な列が多数ある場合、`OnGetAsync` は冗長になる可能性があります。 この機能をコーディングする別の方法については、このチュートリアル シリーズの MVC バージョンの「[動的な LINQ を使ってコードを簡略化する](xref:data/ef-mvc/advanced#dynamic-linq)」を参照してください。

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>列見出しハイパーリンクを Student インデックス ページに追加する

*Students/Index.cshtml* のコードを次のコードに置き換えます。 変更が強調表示されます。

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

上のコードでは以下の操作が行われます。

* `LastName` と `EnrollmentDate` 列見出しにハイパーリンクを追加します。
* この情報を `NameSort` および `DateSort` で使用して、現在の並べ替えの値を含むハイパーリンクを設定します。
* ページ見出しを Index から Students に変更します。
* `Model.Student` を `Model.Students` に変更します。

並べ替えが動作することを確認するには

* アプリを実行し、 **[Students]** タブを選択します。
* 列見出しをクリックします。

## <a name="add-filtering"></a>フィルターの追加

Students インデックス ページにフィルターを追加するには

* テキスト ボックスと [送信] ボタンが、Razor Page に追加されます。 テキスト ボックスは、名と姓で検索文字列を指定します。
* テキスト ボックスの値を使用するようにページ モデルが更新されます。

### <a name="update-the-ongetasync-method"></a>OnGetAsync メソッドの更新

*Students/Index.cshtml.cs* のコードを次のコードに置き換え、フィルターを追加します。

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=28,33,37-41)]

上のコードでは以下の操作が行われます。

* `searchString` パラメーターを `OnGetAsync` メソッドに追加し、`CurrentFilter` プロパティのパラメーター値を保存します。 次のセクションで追加されるテキスト ボックスから検索する文字列値を受け取ります。
* LINQ ステートメントに `Where` 句を追加します。 `Where` 句は、名または姓に検索文字列が含まれている学生のみを選択します。 検索する値がある場合にのみ LINQ ステートメントを実行します。

### <a name="iqueryable-vs-ienumerable"></a>IQueryable/IEnumerable

このコードでは、`IQueryable` オブジェクトに対して `Where` メソッドを呼び出し、フィルターがサーバーで処理されます。 一部のシナリオでは、アプリが `Where` メソッドをメモリ内コレクションの拡張メソッドとして呼び出す場合があります。 たとえば、`_context.Students` が EF Core `DbSet` から `IEnumerable` コレクションを返すリポジトリ メソッドに変更されるとします 結果は、通常同じになりますが、場合によっては異なる場合があります。

たとえば、.NET Framework の `Contains` の実装では、既定では大文字小文字を区別する比較を実行します。 SQL Server で、`Contains` の大文字小文字の区別は、SQL Server インスタンスの照合順序の設定によって決まります。 SQL Server は、既定では大文字小文字を区別しません。 SQLite では、既定で大文字と小文字が区別されます。 `ToUpper` を呼び出して、テストを明示的に大文字小文字を区別しないようにすることができます。

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

上記のコードでは、`Where` メソッドが `IEnumerable` で呼び出された場合でも、SQLite で実行された場合でも、フィルターの大文字と小文字が確実に区別されないようにします。

`Contains` が `IEnumerable` コレクションで呼び出されたときには、.NET Core の実装が使用されます。 `Contains` が `IQueryable` オブジェクトで呼び出されたときには、データベースの実装が使用されます。

通常、パフォーマンス上の理由から、`IQueryable` での `Contains` の呼び出しをお勧めします。 `IQueryable` では、データベース サーバーによってフィルター処理が行われます。 最初に `IEnumerable` を作成する場合は、すべての行がデータベース サーバーから返される必要があります。

`ToUpper` を呼び出すとパフォーマンスが低下します。 `ToUpper` コードは、TSQL SELECT ステートメントの WHERE 句に関数を追加します。 関数が追加されると、オプティマイザーがインデックスを使用できなくなります。 大文字小文字を区別しないように SQL がインストールされている場合、不要な場合は `ToUpper` を呼び出さないようにすることをお勧めします。

詳細については、「[Sqlite プロバイダーで大文字と小文字を区別しないクエリを使用する方法](https://github.com/aspnet/EntityFrameworkCore/issues/11414)」を参照してください。

### <a name="update-the-razor-page"></a>Razor Page の更新

*Pages/Students/Index.cshtml* のコードを置き換えて、 **[検索]** ボタンと類別されたクロムを作成します。

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

前のコードは、`<form>` [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使用して、検索テキスト ボックスとボタンを追加します。 既定では、`<form>` タグ ヘルパーが POST を使用してフォーム データを送信します。 POST を使用すると、URL ではなく HTTP メッセージの本文でパラメーターが渡されます。 HTTP GET を使用すると、フォームのデータはクエリ文字列として URL で渡されます。 クエリ文字列を使用してデータを渡すことにより、ユーザーが URL にブックマークを設定できます。 アクションの結果として更新されない場合、[W3C のガイドライン](https://www.w3.org/2001/tag/doc/whenToUseGet.html)では、Get の使用が推奨されています。

アプリをテストします。

* **[Students]** タブを選択し、検索文字列を入力します。 SQLite を使用している場合、前に示したオプションの `ToUpper` コードを実装した場合にのみ、フィルターの大文字と小文字が区別されません。

* **[Search]** を選択します。

URL に検索文字列が含まれることに注意してください。 次に例を示します。

```
https://localhost:<port>/Students?SearchString=an
```

ブックマークがブックマークに設定されている場合、ブックマークにページの URL と `SearchString` クエリ文字列が含まれます。 `form` タグ内に `method="get"` があると、クエリ文字列が生成されます。

現時点では、列見出しの並べ替えリンクを選択すると、フィルター値が **[Search]** ボックスから失われます。 次のセクションでは、失われたフィルター値は修正されます。

## <a name="add-paging"></a>ページングの追加

このセクションでは、ページングをサポートする `PaginatedList` クラスを作成します。 `PaginatedList` クラスは、`Skip` と `Take` ステートメントを使用して、テーブルのすべての行を取得する代わりに、サーバー上のデータをフィルター処理します。 ページング ボタンを次の図に示します。

![ページング リンクを含む Students インデックス ページ](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>PaginatedList クラスの作成

プロジェクト フォルダーに、次のコードを使用して `PaginatedList.cs` を作成します。

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

前のコードの `CreateAsync` メソッドは、ページ サイズとページ番号を受け取り、適切な `Skip` および `Take` ステートメントを `IQueryable` に適用します。 `IQueryable` で `ToListAsync` が呼び出されると、要求されたページのみを含むリストを返します。 プロパティ `HasPreviousPage` および `HasNextPage` を使用して、 **[Previous]** および **[Next]** ページング ボタンを有効または無効にします。

`CreateAsync` メソッドを使用して `PaginatedList<T>` を作成します。 コンストラクターでは `PaginatedList<T>` オブジェクトを作成できません。コンストラクターでは非同期コードを実行できません。

### <a name="add-paging-to-the-pagemodel-class"></a>PageModel クラスへのページングの追加

*Students/Index.cshtml.cs* のコードを置き換えて、ページングを追加します。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=26,28-29,31,34-41,68-70)]

上のコードでは以下の操作が行われます。

* `Students` プロパティの型を `IList<Student>` から `PaginatedList<Student>` に変更します。
* ページ インデックス、現在の `sortOrder`、`currentFilter` を `OnGetAsync` メソッド シグネチャに追加します。
* 並べ替え順序を CurrentSort プロパティに保存します。
* 新しい検索文字列がある場合に、ページ インデックスを 1 にリセットします。
* `PaginatedList` クラスを使用して、Student エンティティを取得します。

`OnGetAsync` で受け取るパラメーターはすべて、次の場合に null になります。

* **Students** リンクからページが呼び出されます。
* ユーザーは、ページングまたは並べ替えのリンクをクリックしていません。

ページングのリンクをクリックすると、ページ インデックス変数に表示するページ番号が含まれます。

`CurrentSort` プロパティでは、現在の並べ替え順序を含む Razor Page を提供します。 ページングの中に並べ替え順序を保持するために、ページングリンクに、現在の並べ替え順序を含まれている必要があります。

`CurrentFilter` プロパティでは、現在のフィルター文字列を含む Razor Page を提供します。 `CurrentFilter` 値:

* ページングの中に、フィルターの設定を維持するために、ページング リンクに含まれている必要があります。
* ページがリダイレクトされるときに、テキスト ボックスに復元される必要があります。

ページングの中に検索文字列を変更する場合は、ページが 1 にリセットされます。 新しいフィルターのために別のデータが表示されるため、ページを 1 にリセットする必要があります。 検索値が入力され、 **[Submit]** が選択された場合:

  * 検索文字列が変更されます。
  * `searchString` パラメーターは null ではありません。

  `PaginatedList.CreateAsync` メソッドが、ページングをサポートするコレクション型の学生の 1 つのページに学生クエリを変換します。 その 1 つの学生のページが Razor Page に渡されます。

  `PaginatedList.CreateAsync` 呼び出しの `pageIndex` の後の 2 つの疑問符は、[null 合体演算子](/dotnet/csharp/language-reference/operators/null-conditional-operator)を表します。 Null 合体演算子は、null 許容型の既定値を定義します。 式 `(pageIndex ?? 1)` は、値がある場合に、`pageIndex` の値を返すことを意味します。 `pageIndex` に値がない場合は、1 を返します。

### <a name="add-paging-links-to-the-razor-page"></a>Razor Page にページングのリンクを追加する

*Students/Index.cshtml* のコードを次のコードに置き換えます。 変更が強調表示されています。

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

列ヘッダー リンクでは、クエリ文字列を使用して現在の検索文字列を `OnGetAsync` メソッドに渡します。

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

タグ ヘルパーによってページング ボタンが表示されます。

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

アプリを実行して [Students] ページに移動します。

* 異なる並べ替え順でページングのリンクをクリックし、ページングが機能することを確認します。
* 検索文字列を入力して、ページングを試し、並べ替えとフィルター処理を使用してもページングが正しく機能することを確認します。

![ページング リンクを含む Students インデックス ページ](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>グループの追加

このセクションでは、登録日ごとに何人の学生が登録したかを表示する About ページを作成します。 更新では、グループ化を使用し、次の手順が含まれています。

* **About** ページで使用されるデータのビュー モデルを作成します。
* ビュー モデルを使用するように About ページを更新します。

### <a name="create-the-view-model"></a>ビュー モデルを作成する

*Models/SchoolViewModels* フォルダーを作成します。

次のコードを使用して、*SchoolViewModels/EnrollmentDateGroup.cs* を作成します。

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-razor-page"></a>Razor Page の作成

次のコードを使用して、*Pages/About.cshtml* ファイルを作成します。

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>ページ モデルの作成

次のコードを使用して、*Pages/About.cshtml.cs* ファイルを作成します。

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

LINQ ステートメントは、登録日で受講者エンティティをグループ化し、各グループ内のエンティティの数を計算して、結果を `EnrollmentDateGroup` ビュー モデル オブジェクトのコレクションに格納します。

アプリを実行して [About] ページに移動します。 登録の日付ごとの学生の数が、テーブルに表示されます。

![About ページ](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>次の手順

次のチュートリアルでは、アプリは移行を使用してデータ モデルを更新します。

> [!div class="step-by-step"]
> [前のチュートリアル](xref:data/ef-rp/crud)
> [次のチュートリアル](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このチュートリアルでは、並べ替え、フィルター処理、グループ化、ページング、機能が追加されます。

次の図は、完成したページを示しています。 列見出しはクリックできるリンクとなっており、クリックすると列が並べ替えられます。 列見出しを繰り返しクリックすると、昇順と降順の並べ替え順序が切り替えられます。

![Students インデックス ページ](sort-filter-page/_static/paging.png)

解決できない問題が発生した場合は、[完成したアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)をダウンロードしてください。

## <a name="add-sorting-to-the-index-page"></a>インデックス ページに並べ替えを追加する

*Students/Index.cshtml.cs* `PageModel` に文字列を追加し並べ替えのパラメーターを格納します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

*Students/Index.cshtml.cs* `OnGetAsync` を次のコードで更新します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

前のコードは、URL 内のクエリ文字列から `sortOrder` パラメーターを受け取ります。 (クエリ文字列を含む) URL が[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)によって生成されます。

`sortOrder` パラメーターは "Name" または "Date" です。 `sortOrder` パラメーターの後に必要に応じて "_desc" を続け、降順を指定します。 既定の並べ替え順序は昇順です。

インデックス ページが、**Students** リンクから要求された場合、クエリ文字列はありません。 学生は、姓の昇順で表示されます。 `switch` ステートメントでは姓の昇順が既定値 (フォールスルー ケース) です。 ユーザーが列見出しリンクをクリックすると、適切な `sortOrder` 値がクエリ文字列値で提供されます。

`NameSort` および `DateSort` は、Razor Page で、適切なクエリ文字列値を持つ列見出しのハイパーリンクを構成するために使用されます。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

次のコードは、C# の条件演算子 [?:](/dotnet/csharp/language-reference/operators/conditional-operator) を含んでいます。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

最初の行は、`sortOrder` が null または空の場合に、`NameSort` を "name_desc" に設定することを指定します。 `sortOrder` が null または空**ではない**場合、`NameSort` は空の文字列に設定されます。

`?: operator` は三項演算子とも呼ばれます。

これらの 2 つのステートメントを使用して、次のようにページで列見出しのハイパーリンクを設定することができます。

| 既定の並べ替え順 | 姓のハイパーリンク | 日付のハイパーリンク |
|:--------------------:|:-------------------:|:--------------:|
| 姓の昇順 | descending        | ascending      |
| 姓の降順 | ascending           | ascending      |
| 日付の昇順       | ascending           | descending     |
| 日付の降順      | ascending           | ascending      |

このメソッドは、並べ替える列を指定するのに LINQ to Entities を使用します。 このコードは、switch ステートメントの前に `IQueryable<Student>` を初期化し、switch ステートメントでそれを変更します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 `IQueryable` が作成または変更されるときには、クエリは、データベースに送信されません。 クエリは、`IQueryable` オブジェクトがコレクションに変換されるまで実行されません。 `IQueryable` は、`ToListAsync` などのメソッドを呼び出すことで、コレクションに変換されます。 そのため、`IQueryable` コードの結果として、次のステートメントまで実行されない 1 つのクエリになります。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

並べ替え可能な列が多数ある場合、`OnGetAsync` は冗長になる可能性があります。

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>列見出しハイパーリンクを Student インデックス ページに追加する

*Students/Index.cshtml* のコードを次の強調表示されたコードに置き換えます。

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

上のコードでは以下の操作が行われます。

* `LastName` と `EnrollmentDate` 列見出しにハイパーリンクを追加します。
* この情報を `NameSort` および `DateSort` で使用して、現在の並べ替えの値を含むハイパーリンクを設定します。

並べ替えが動作することを確認するには

* アプリを実行し、 **[Students]** タブを選択します。
* **[Last Name]** をクリックします。
* **[Enrollment Date]** をクリックします。

コードの理解を深めるために、次の手順を実行します。

* *Students/Index.cshtml.cs* で、`switch (sortOrder)` にブレークポイントを設定します。
* `NameSort` と `DateSort` のウォッチを追加します。
* *Students/Index.cshtml* で、`@Html.DisplayNameFor(model => model.Student[0].LastName)` にブレークポイントを設定します。

デバッガーの手順を実行します。

## <a name="add-a-search-box-to-the-students-index-page"></a>Students インデックス ページに [検索] ボックスを追加する

Students インデックス ページにフィルターを追加するには

* テキスト ボックスと [送信] ボタンが、Razor Page に追加されます。 テキスト ボックスは、名と姓で検索文字列を指定します。
* テキスト ボックスの値を使用するようにページ モデルが更新されます。

### <a name="add-filtering-functionality-to-the-index-method"></a>Index メソッドにフィルター機能を追加する

*Students/Index.cshtml.cs* `OnGetAsync` を次のコードで更新します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

上のコードでは以下の操作が行われます。

* `searchString` パラメーターを `OnGetAsync` メソッドに追加します。 次のセクションで追加されるテキスト ボックスから検索する文字列値を受け取ります。
* LINQ ステートメントに `Where` 句を追加します。 `Where` 句は、名または姓に検索文字列が含まれている学生のみを選択します。 検索する値がある場合にのみ LINQ ステートメントを実行します。

メモ:前のコードは、`IQueryable` オブジェクトに対して `Where` メソッドを呼び出し、フィルターがサーバーで処理されます。 一部のシナリオでは、アプリが `Where` メソッドをメモリ内コレクションの拡張メソッドとして呼び出す場合があります。 たとえば、`_context.Students` が EF Core `DbSet` から `IEnumerable` コレクションを返すリポジトリ メソッドに変更されるとします 結果は、通常同じになりますが、場合によっては異なる場合があります。

たとえば、.NET Framework の `Contains` の実装では、既定では大文字小文字を区別する比較を実行します。 SQL Server で、`Contains` の大文字小文字の区別は、SQL Server インスタンスの照合順序の設定によって決まります。 SQL Server は、既定では大文字小文字を区別しません。 `ToUpper` を呼び出して、テストを明示的に大文字小文字を区別しないようにすることができます。

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

上記のコードは、コードが `IEnumerable` を使用するように変更された場合、結果で大文字小文字が区別されないようにします。 `Contains` が `IEnumerable` コレクションで呼び出されたときには、.NET Core の実装が使用されます。 `Contains` が `IQueryable` オブジェクトで呼び出されたときには、データベースの実装が使用されます。 リポジトリから `IEnumerable` を返すと、パフォーマンスが大幅に低下する可能性があります。

1. DB サーバーからすべての行が返されます。
1. アプリケーションで返されたすべての行にフィルターが適用されます。

`ToUpper` を呼び出すとパフォーマンスが低下します。 `ToUpper` コードは、TSQL SELECT ステートメントの WHERE 句に関数を追加します。 関数が追加されると、オプティマイザーがインデックスを使用できなくなります。 大文字小文字を区別しないように SQL がインストールされている場合、不要な場合は `ToUpper` を呼び出さないようにすることをお勧めします。

### <a name="add-a-search-box-to-the-student-index-page"></a>Students インデックス ページに [検索] ボックスを追加する

*Pages/Students/Index.cshtml* で、次の強調表示されたコードを追加し、 **[検索]** ボタンと各種のクロムを追加します。

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

前のコードは、`<form>` [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使用して、検索テキスト ボックスとボタンを追加します。 既定では、`<form>` タグ ヘルパーが POST を使用してフォーム データを送信します。 POST を使用すると、URL ではなく HTTP メッセージの本文でパラメーターが渡されます。 HTTP GET を使用すると、フォームのデータはクエリ文字列として URL で渡されます。 クエリ文字列を使用してデータを渡すことにより、ユーザーが URL にブックマークを設定できます。 アクションの結果として更新されない場合、[W3C のガイドライン](https://www.w3.org/2001/tag/doc/whenToUseGet.html)では、Get の使用が推奨されています。

アプリをテストします。

* **[Students]** タブを選択し、検索文字列を入力します。
* **[Search]** を選択します。

URL に検索文字列が含まれることに注意してください。

```html
http://localhost:5000/Students?SearchString=an
```

ブックマークがブックマークに設定されている場合、ブックマークにページの URL と `SearchString` クエリ文字列が含まれます。 `form` タグ内に `method="get"` があると、クエリ文字列が生成されます。

現時点では、列見出しの並べ替えリンクを選択すると、フィルター値が **[Search]** ボックスから失われます。 次のセクションでは、失われたフィルター値は修正されます。

## <a name="add-paging-functionality-to-the-students-index-page"></a>Students インデックス ページにページング機能を追加する

このセクションでは、ページングをサポートする `PaginatedList` クラスを作成します。 `PaginatedList` クラスは、`Skip` と `Take` ステートメントを使用して、テーブルのすべての行を取得する代わりに、サーバー上のデータをフィルター処理します。 ページング ボタンを次の図に示します。

![ページング リンクを含む Students インデックス ページ](sort-filter-page/_static/paging.png)

プロジェクト フォルダーに、次のコードを使用して `PaginatedList.cs` を作成します。

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

前のコードの `CreateAsync` メソッドは、ページ サイズとページ番号を受け取り、適切な `Skip` および `Take` ステートメントを `IQueryable` に適用します。 `IQueryable` で `ToListAsync` が呼び出されると、要求されたページのみを含むリストを返します。 プロパティ `HasPreviousPage` および `HasNextPage` を使用して、 **[Previous]** および **[Next]** ページング ボタンを有効または無効にします。

`CreateAsync` メソッドを使用して `PaginatedList<T>` を作成します。 コンストラクターは、`PaginatedList<T>` オブジェクトを作成できません。コンストラクターは、非同期コードを実行できません。

## <a name="add-paging-functionality-to-the-index-method"></a>Index メソッドにページング機能を追加する

*Students/Index.cshtml.cs* で、`Student` の型を `IList<Student>` から `PaginatedList<Student>` に更新します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

*Students/Index.cshtml.cs* `OnGetAsync` を次のコードで更新します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

上記のコードは、ページ インデックス、現在の `sortOrder`、および `currentFilter` をメソッド シグネチャに追加します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

すべてのパラメーターは、次のような場合に null になります。

* **Students** リンクからページが呼び出されます。
* ユーザーは、ページングまたは並べ替えのリンクをクリックしていません。

ページングのリンクをクリックすると、ページ インデックス変数に表示するページ番号が含まれます。

`CurrentSort` は、現在の並べ替え順序を含む Razor Page を提供します。 ページングの中に並べ替え順序を保持するために、ページングリンクに、現在の並べ替え順序を含まれている必要があります。

`CurrentFilter` は、現在のフィルター文字列を含む Razor Page を提供します。 `CurrentFilter` 値:

* ページングの中に、フィルターの設定を維持するために、ページング リンクに含まれている必要があります。
* ページがリダイレクトされるときに、テキスト ボックスに復元される必要があります。

ページングの中に検索文字列を変更する場合は、ページが 1 にリセットされます。 新しいフィルターのために別のデータが表示されるため、ページを 1 にリセットする必要があります。 検索値が入力され、 **[Submit]** が選択された場合:

* 検索文字列が変更されます。
* `searchString` パラメーターは null ではありません。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync` メソッドが、ページングをサポートするコレクション型の学生の 1 つのページに学生クエリを変換します。 その 1 つの学生のページが Razor Page に渡されます。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

`PaginatedList.CreateAsync` の 2 つの疑問符は、[null 合体演算子](/dotnet/csharp/language-reference/operators/null-conditional-operator)を表します。 Null 合体演算子は、null 許容型の既定値を定義します。 式 `(pageIndex ?? 1)` は、値がある場合に、`pageIndex` の値を返すことを意味します。 `pageIndex` に値がない場合は、1 を返します。

## <a name="add-paging-links-to-the-student-razor-page"></a>Student Razor Page にページングのリンクを追加する

*Students/Index.cshtml* 内のマークアップを更新する 変更が強調表示されています。

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

列ヘッダー リンクは、ユーザーがフィルターの結果内で並べ替えられるように、クエリ文字列を使用して現在の検索文字列を `OnGetAsync` メソッドに渡します。

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

タグ ヘルパーによってページング ボタンが表示されます。

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

アプリを実行して [Students] ページに移動します。

* 異なる並べ替え順でページングのリンクをクリックし、ページングが機能することを確認します。
* 検索文字列を入力して、ページングを試し、並べ替えとフィルター処理を使用してもページングが正しく機能することを確認します。

![ページング リンクを含む Students インデックス ページ](sort-filter-page/_static/paging.png)

コードの理解を深めるために、次の手順を実行します。

* *Students/Index.cshtml.cs* で、`switch (sortOrder)` にブレークポイントを設定します。
* `NameSort`、`DateSort`、`CurrentSort`、`Model.Student.PageIndex` のウォッチを追加します。
* *Students/Index.cshtml* で、`@Html.DisplayNameFor(model => model.Student[0].LastName)` にブレークポイントを設定します。

デバッガーの手順を実行します。

## <a name="update-the-about-page-to-show-student-statistics"></a>学生の統計情報を表示するように [About] ページを更新します。

このステップで、*Pages/About.cshtml* が更新され、登録日付ごとに登録した学生の数を表示します。 更新では、グループ化を使用し、次の手順が含まれています。

* **About** ページで使用されるデータのビュー モデルを作成します。
* ビュー モデルを使用するように About ページを更新します。

### <a name="create-the-view-model"></a>ビュー モデルを作成する

*SchoolViewModels* フォルダーを *Models* フォルダー内に作成します。

次のコードを使用して、*SchoolViewModels* フォルダー内に *EnrollmentDateGroup.cs* を追加します。

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>About ページ モデルを更新する

ASP.NET Core 2.2 の Web テンプレートには、About ページが含まれていません。 ASP.NET Core 2.2 を使っている場合は、About Razor ページを作成します。

次のコードを使用して、*Pages/About.cshtml.cs* を更新します。

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

LINQ ステートメントは、登録日で受講者エンティティをグループ化し、各グループ内のエンティティの数を計算して、結果を `EnrollmentDateGroup` ビュー モデル オブジェクトのコレクションに格納します。

### <a name="modify-the-about-razor-page"></a>About Razor Page ページを変更します。

*Pages/About.cshtml* ファイルのコードを次のコードに置き換えます。

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

アプリを実行して [About] ページに移動します。 登録の日付ごとの学生の数が、テーブルに表示されます。

解決できない問題が発生した場合は、[このステージの完成したアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting)をダウンロードしてください。

![About ページ](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core 2.x ソースのデバッグ](https://github.com/aspnet/AspNetCore.Docs/issues/4155)
* [このチュートリアルの YouTube バージョン](https://www.youtube.com/watch?v=MDs7PFpoMqI)

次のチュートリアルでは、アプリは移行を使用してデータ モデルを更新します。

> [!div class="step-by-step"]
> [前へ](xref:data/ef-rp/crud)
> [次へ](xref:data/ef-rp/migrations)

::: moniker-end

