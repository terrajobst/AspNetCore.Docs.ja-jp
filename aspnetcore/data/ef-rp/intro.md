---
title: ASP.NET Core での Entity Framework Core を使用した Razor ページ - チュートリアル 1/8
author: tdykstra
description: Entity Framework Core を使用して Razor ページ アプリを作成する方法について説明します
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/22/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 5e81b58ebec2d9a50784facd5425f0e7c3524e10
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583445"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>ASP.NET Core での Entity Framework Core を使用した Razor ページ - チュートリアル 1/8

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

これは、ASP.NET Core Razor Pages アプリで Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。 このチュートリアルでは、架空の Contoso University の Web サイトを構築します。 サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。

[完成したアプリをダウンロードまたは表示します。](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>必須コンポーネント

* Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>データベース エンジン

Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。

Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。

SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。

## <a name="troubleshooting"></a>トラブルシューティング

解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。 ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。

## <a name="the-sample-app"></a>サンプル アプリ

このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。 ユーザーは学生、講座、講師の情報を見たり、更新したりできます。 チュートリアルで作成する画面は次のようになります。

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。 このチュートリアルでは、UI をカスタマイズする方法ではなく、主に EF Core の使用方法について説明します。

ページの上部にあるリンクを使用して、完成したプロジェクトのソース コードを取得します。 *cu30* フォルダーには、チュートリアルの ASP.NET Core 3.0 バージョン用のコードが含まれています。 チュートリアル 1-7 のコードの状態を反映するファイルは、*cu30snapshots* フォルダー内にあります。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

完成したプロジェクトをダウンロードした後にアプリを実行するには:

* 名前に *SQLite* がある 3 つのファイルと 1 つのフォルダーを削除します。
* プロジェクトをビルドします。
* パッケージ マネージャー コンソール (PMC) で、次のコマンドを実行します。

  ```powershell
  Update-Database
  ```

* プロジェクトを実行して、データベースをシードします。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

完成したプロジェクトをダウンロードした後にアプリを実行するには:

* *ContosoUniversity.csproj* を削除し、*ContosoUniversitySQLite.csproj* の名前を *ContosoUniversity.csproj* に変更します。
* *Startup.cs* を削除し、*StartupSQLite.cs* の名前を *Startup.cs* に変更します。
* *appSettings.json* を削除し、*appSettingsSQLite.json* の名前を *appSettings.json* に変更します。
* *Migrations* フォルダーを削除し、*MigrationsSQL* の名前を *Migrations* に変更します。
* プロジェクトをビルドします。
* プロジェクト フォルダーのコマンド プロンプトで、次のコマンドを実行します。

  ```console
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  dotnet ef database update
  ```

* SQLite ツールで、次の SQL ステートメントを実行します。

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* プロジェクトを実行して、データベースをシードします。

---

## <a name="create-the-web-app-project"></a>Web アプリプロジェクトを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* プロジェクトに *ContosoUniversity* という名前を付けます。 コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。
* ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択してから、 **[Web アプリケーション]** を選択します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。

* 次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。

  ```console
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>サイトのスタイルを設定する

*Pages/Shared/_Layout.cshtml* を更新して、サイト ヘッダー、フッター、およびメニューを設定します。

* "ContosoUniversity" をすべて "Contoso University" に変更します。 これは 3 回出てきます。

* **Home** メニューと **Privacy** メニューのエントリを削除し、**About**、**Students**、**Courses**、**Instructors**、**Departments** のエントリを追加します。

変更が強調表示されます。

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET Core に関するテキストをこのアプリに関するテキストに変更します。

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

アプリを実行して、ホームページが表示されることを確認します。

## <a name="the-data-model"></a>データ モデル

以降のセクションでは、データ モデルを作成します。

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。

## <a name="the-student-entity"></a>Student エンティティ

![Student エンティティの図](intro/_static/student-entity.png)

* プロジェクト フォルダー内に *Models* フォルダーを作成します。 

* 次のコードを使用して、*Models/Student.cs* を作成します。

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。 既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。 そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。

`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。 ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。 この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。 たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。 

データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。 たとえば、Student 行の ID が 1 であるとします。 関連する Enrollment 行の StudentID は 1 になります。 StudentID は、Enrollment テーブルの*外部キー*です。 

`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。 他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。 `ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。

## <a name="the-enrollment-entity"></a>Enrollment エンティティ

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

以下のコードを使用して、*Models/Enrollment.cs* を作成します。

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。 実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。 このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。 `classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。

`Grade` プロパティは `enum` です。 `Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/) であることを示します。 null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。

`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。 `Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。

`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。 `Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。

EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。 たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。 外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。 たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。

## <a name="the-course-entity"></a>Course エンティティ

![Course エンティティの図](intro/_static/course-entity.png)

以下のコードを使用して、*Models/Course.cs* を作成します。

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments` プロパティはナビゲーション プロパティです。 1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。

`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。

プロジェクトをビルドし、コンパイラ エラーがないことを検証します。

## <a name="scaffold-student-pages"></a>Student ページのスキャフォールディング

このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。

* EF Core *コンテキスト* クラス。 コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。 これは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生します。
* `Student` エンティティの CRUD (Create, Read, Update, and Delete/作成、読み取り、更新と削除) 操作を処理する Razor ページ。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Pages* フォルダー内に *Students* フォルダーを作成します。
* **ソリューション エクスプローラー**で、 *[Pages/Students]* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。
* **[スキャフォールディングを追加]** ダイアログで、 **[Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用する Razor Pages (CRUD)\)** > **[追加]** の順に選択します。
* **[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。
  * **[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。
  * **Data context class** 行で、 **+** (+) 記号を選択します。
  * データ コンテキスト名を *ContosoUniversity.Models.ContosoUniversityContext* から *ContosoUniversity.Data.SchoolContext* に変更します。
  * **[追加]** を選びます。

次のパッケージが自動的にインストールされます。

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。

  ```console
  dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools --version 3.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
  dotnet add package Microsoft.Extensions.Logging.Debug --version 3.0.0-*
  ```

  スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。 アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。

* *Pages/Students* フォルダーを作成します。

* 次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。

  ```console
  dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
  ```

* 次のコマンドを実行して、Student ページをスキャフォールディングします。

  **Windows の場合**

  ```console
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **macOS または Linux の場合**

  ```console
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

前の手順で問題が発生した場合は、プロジェクトをビルドし、スキャフォールディング ステップを再試行してください。

スキャフォールディング プロセスは次のとおりです。

* *Pages/Students* フォルダーに Razor ページを作成します。
  * *Create.cshtml* と *Create.cshtml.cs*
  * *Delete.cshtml* と *Delete.cshtml.cs*
  * *Details.cshtml* と *Details.cshtml.cs*
  * *Edit.cshtml* と *Edit.cshtml.cs*
  * *Index.cshtml* と *Index.cshtml.cs*
* *Data/SchoolContext.cs* を作成します。
* *Startup.cs* の依存関係の挿入にコンテキストを追加します。
* データベース接続文字列を *appsettings.json* に追加します。

## <a name="database-connection-string"></a>データベース接続文字列

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。 既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*CU.db* という名前の SQLite データベース ファイルを指すように接続文字列を変更します。

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>データベース コンテキスト クラスを更新する

定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。 コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。 コンテキストによって、データ モデルに含めるエンティティが指定されます。 このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。

次のコードを使用して *SchoolContext.cs* を更新します。

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

強調表示されたコードで、各エンティティ セットの [DbSet\< TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。 EF Core 用語で:

* エンティティ セットは通常、データベース テーブルに対応します。
* エンティティはテーブル内の行に対応します。

エンティティ セットには複数のエンティティが含まれているため、DBSet プロパティは複数形の名前にする必要があります。 スキャフォールディング ツールによって `Student` DBSet を作成したので、このステップでこれを複数形の `Students` に変更します。 

Razor Pages コードを新しい DBSet 名と一致させるには、プロジェクト全体で `_context.Student` を `_context.Students` にグローバルに変更します。  8 回の出現があります。

プロジェクトをビルドし、コンパイラ エラーがないことを確認します。

## <a name="startupcs"></a>Startup.cs

ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。 サービス (EF Core データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。 これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。 データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。

スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* `ConfigureServices` では、強調表示された行がスキャフォールダーによって追加されました。

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* `ConfigureServices` では、スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。 ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。

## <a name="create-the-database"></a>データベースの作成

データベースが存在しない場合は、*Program.cs* を更新して作成します。

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。 データベースが存在しない場合は、データベースとスキーマが作成されます。 `EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。

* データベースを削除します。 既存のデータはすべて失われます。
* データ モデルを変更します。 たとえば、`EmailAddress` フィールドを追加します。
* アプリを実行します。
* `EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。

このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。 データベースに入力されたデータを保持する必要がある場合は、状況は異なります。 その場合は、移行を使用します。

この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。 `EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。

### <a name="test-the-app"></a>アプリのテスト

* アプリを実行します。
* **[Students]** リンクを選択し、 **[新規作成]** を選択します。
* [編集]、[詳細]、および [削除] の各リンクをテストします。

## <a name="seed-the-database"></a>データベースのシード

`EnsureCreated` メソッドは、空のデータベースを作成します。 このセクションでは、データベースにテスト データを入力するコードを追加します。

次のコードを使用して、*Data/DbInitializer.cs* を作成します。

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  このコードは、データベースに学生が存在するかどうかを確認します。 学生が存在しない場合は、テスト データがデータベースに追加されます。 パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。

* *Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* アプリを実行し、前の手順で作成した学生レコードを削除して、アプリを停止します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。

---

* アプリを再起動します。

* Students ページを選択すると、シードされたデータが表示されます。

## <a name="view-the-database"></a>データベースを表示する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。
* SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。 前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。
* **[Tables]\(テーブル\)** ノードを展開します。
* **[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。
* **Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。 データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。

---

## <a name="asynchronous-code"></a>非同期コード

ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。

Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。 その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。 同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。 非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。 結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。

非同期コードが実行時に発生させるオーバーヘッドは少量です。 トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。

次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* キーワード `async` は次のことをコンパイラに指示します。
  * メソッド本文の一部にコールバックを生成する。
  * 返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。
* 戻り値の型 `Task<T>` は進行中の作業を表します。
* キーワード `await` により、コンパイラはメソッドを 2 つに分割します。 最初の部分は、非同期で開始される操作で終わります。 2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。
* `ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。

EF Core を利用する非同期コードの記述で注意すべき点:

* クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。 これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。 `var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。
* EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。
* 非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。

.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="step-by-step"]
> [次のチュートリアル](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Contoso University のサンプル Web アプリでは、Entity Framework (EF) Core を使用して ASP.NET Core Razor Pages アプリを作成する方法を示しています。

このサンプル アプリは架空の Contoso University の Web サイトです。 学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。 このページは、Contoso University のサンプル アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です。

[完成したアプリをダウンロードまたは表示します。](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

[Razor ページ](xref:razor-pages/index)に関する知識。 Razor ページのプログラミングが初めての場合、このシリーズを始める前に[こちらの入門編](xref:tutorials/razor-pages/razor-pages-start)を完了してください。

## <a name="troubleshooting"></a>トラブルシューティング

解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と自分のコードを比較することで解決策がわかります。 ヘルプが必要なときは、[StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) について質問を投稿することをお勧めします。

## <a name="the-contoso-university-web-app"></a>Contoso University Web アプリ

このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。

ユーザーは学生、講座、講師の情報を見たり、更新したりできます。 チュートリアルで作成する画面は次のようになります。

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

このサイトの UI スタイルは、組み込みのテンプレートによって生成されるものに類似しています。 このチュートリアルでは、UI ではなく、EF Core と Razor ページに重点を置きます。

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>ContosoUniversity Razor Pages Web アプリを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。
* 新しい ASP.NET Core Web アプリケーションを作成します。 プロジェクトに **ContosoUniversity** という名前を付けます。 このプロジェクトに *ContosoUniversity* という名前を付けることは重要です。そうすることでコードをコピーしたり貼り付けるときに名前空間が一致します。
* ドロップダウン リストで **[ASP.NET Core 2.1]** を選択してから、 **[Web アプリケーション]** を選択します。

前の手順の画像については、「[Razor Web アプリの作成](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)」を参照してください。
アプリを実行します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>サイトのスタイルを設定する

変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。 *Pages/Shared/_Layout.cshtml* に次の変更を加えます。

* "ContosoUniversity" をすべて "Contoso University" に変更します。 これは 3 回出てきます。

* メニュー エントリとして「**Students**」、「**Courses**」、「**Instructors**」、「**Departments**」を追加し、「**Contact**」を削除します。

変更が強調表示されます。 (マークアップは一部表示されて*いません*。)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリに関するテキストに変更します。

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>データ モデルを作成する

Contoso University アプリのエンティティ クラスを作成します。 次の 3 つのエンティティから始めます。

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

`Student` エンティティと `Enrollment` エンティティの間には一対多の関係があります。 `Course` エンティティと `Enrollment` エンティティの間には一対多の関係があります。 1 人の学生をさまざまな講座に登録できます。 1 つの講座に任意の数の学生を登録できます。

次のセクションでは、エンティティごとにクラスを作成します。

### <a name="the-student-entity"></a>Student エンティティ

![Student エンティティの図](intro/_static/student-entity.png)

*Models* フォルダーを作成します。 以下のコードを使用して、*Models* フォルダーで、*Student.cs* という名前のクラス ファイルを作成します。

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID` プロパティは、このクラスに相当するデータベース (DB) テーブルの主キー列になります。 既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。 `classnameID` の `classname` はクラスの名前です。 代わりの自動的に認識される主キーは、前の例の `StudentID` です。

`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。 ナビゲーション プロパティは、このエンティティに関連する他のエンティティにリンクします。 この例では、`Student entity` の `Enrollments` プロパティによって、その `Student` に関連するすべての `Enrollment` エンティティが保持されます。 たとえば、DB 内のある Student 行に 2 つ関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。 関連する `Enrollment` 行とは、その学生の主キー値を `StudentID` 列に含む行です。 たとえば、ID=1 の学生には、`Enrollment` テーブルに行が 2 つあるとします。 その `Enrollment` テーブルには、`StudentID` = 1 の行が 2 つあります。 `StudentID` は `Enrollment` テーブルの外部キーであり、`Student` テーブルでその学生を指定します。

あるナビゲーション プロパティが複数のエンティティを保持できる場合、そのナビゲーション プロパティは `ICollection<T>` のようなリスト型にする必要があります。 `ICollection<T>` を指定するか、`List<T>` や `HashSet<T>` のような型を指定できます。 `ICollection<T>` を使用する場合、EF Core で `HashSet<T>` コレクションが既定で作成されます。 複数のエンティティを保持するナビゲーション プロパティは、多対多または一対多の関係から発生します。

### <a name="the-enrollment-entity"></a>Enrollment エンティティ

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

以下のコードを使用して、*Models* フォルダーで、*Enrollment.cs* を作成します。

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` プロパティは主キーです。 このエンティティでは、`Student` エンティティのような `ID` ではなく、`classnameID` パターンを使用します。 一般的に、開発者は 1 つのパターンを選択し、データ モデル全体でそれを使用します。 後のチュートリアルでは、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装します。

`Grade` プロパティは `enum` です。 `Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。 null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。

`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。 `Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。 `Student` エンティティは、複数の `Enrollment` エンティティが含まれる `Student.Enrollments` ナビゲーション プロパティとは異なります。

`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。 `Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。

EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。 たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーは `ID` であるからです。 外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。 たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。

### <a name="the-course-entity"></a>Course エンティティ

![Course エンティティの図](intro/_static/course-entity.png)

以下のコードを使用して、*Models* フォルダーで、*Course.cs* を作成します。

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments` プロパティはナビゲーション プロパティです。 1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。

`DatabaseGenerated` 属性によって、アプリで主キーを指定できます。DB に生成させるのではありません。

## <a name="scaffold-the-student-model"></a>Student モデルをスキャホールディングする

このセクションでは、Student モデルがスキャフォールディングされます。 つまり、スキャフォールディング ツールにより、Student モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。

* プロジェクトをビルドします。
* *Pages/Students* フォルダーを作成します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューション エクスプローラー**で、 *[Pages/Students]* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。
* **[スキャフォールディングを追加]** ダイアログで、 **[Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用する Razor Pages (CRUD)\)** > **[追加]** の順に選択します。

**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。

* **[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。
* **[データ コンテキスト クラス]** 行で **+** (+) 記号を選択し、生成された名前を **ContosoUniversity.Models.SchoolContext** に変更します。
* **[データ コンテキスト クラス]** ドロップダウンで、 **[ContosoUniversity.Models.SchoolContext]** を選択します。
* **[追加]** を選びます。

![CRUD ダイアログ](intro/_static/s1.png)

前の手順に問題がある場合は、「[ムービー モデルのスキャフォールディング](xref:tutorials/razor-pages/model#scaffold-the-movie-model)」を参照してください。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

次のコマンドを実行して、Student モデルをスキャホールディングします。

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

スキャフォールディングのプロセスが作成され、次のファイルが変更されます。

### <a name="files-created"></a>作成されたファイル

* *Pages/Students* 作成、削除、詳細、編集、インデックス。
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>ファイルの更新

* *Startup.cs*:このファイルに対する変更を次のセクションで詳しく説明します。
* *appsettings.json*:ローカル データベースへの接続に使用される接続文字列を追加します。

## <a name="examine-the-context-registered-with-dependency-injection"></a>依存関係挿入に登録されるコンテキストを調べる

ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。 サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。 これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。 db コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。

スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。

*Startup.cs* の `ConfigureServices` メソッドを調べます。 強調表示された行は、スキャフォールダーによって追加されました。

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=5-6)]

[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。 ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。

## <a name="update-main"></a>main を更新する

*Program.cs* で、次を実行するように `Main` メソッドを変更します。

* 依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。
* [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) を呼び出します。
* `EnsureCreated` メソッドが完了したら、コンテキストを破棄します。

次は、更新された *Program.cs* ファイルのコードです。

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` で、コンテキストのデータベースが存在することを確認します。 存在する場合、アクションは何も実行されません。 存在しない場合は、データベースとそのすべてのスキーマが作成されます。 `EnsureCreated` は、データベースの作成に移行を使用しません。 `EnsureCreated` で作成されたデータベースは、後で移行を使用して更新することはできません。

アプリの起動時に `EnsureCreated` が呼び出され、次のワークフローが可能になります。

* DB を削除します。
* DB スキーマを変更します (たとえば、`EmailAddress` フィールドを追加します)。
* アプリを実行します。
* `EnsureCreated` によって、`EmailAddress` 列を含む DB が作成されます。

`EnsureCreated` は、スキーマが急速に進化している開発の早期に便利です。 このチュートリアルの後半では、DB は削除され、移行が使用されます。

### <a name="test-the-app"></a>アプリのテスト

アプリを実行し、Cookie ポリシーに同意します。 このアプリで個人情報が保持されることはありません。 Cookie ポリシーについては、[EU の一般的なデータ保護規制 (GDPR) のサポート](xref:security/gdpr)に関するページを参照してください。

* **[Students]** リンクを選択し、 **[新規作成]** を選択します。
* [編集]、[詳細]、および [削除] の各リンクをテストします。

## <a name="examine-the-schoolcontext-db-context"></a>SchoolContext DB コンテキストを確認する

所与のデータ モデルの EF Core 機能を調整するメイン クラスは、DB コンテキスト クラスです。 データ コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。 データ コンテキストによって、データ モデルに含めるエンティティが指定されます。 このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。

次のコードを使用して *SchoolContext.cs* を更新します。

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

強調表示されたコードで、各エンティティ セットの [DbSet\< TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。 EF Core 用語で:

* エンティティ セットは通常、DB テーブルに対応します。
* エンティティはテーブル内の行に対応します。

`DbSet<Enrollment>` と `DbSet<Course>` は省略可能です。 EF Core にはそれらが暗黙的に含まれます。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。 このチュートリアルでは、`SchoolContext` に `DbSet<Enrollment>` と `DbSet<Course>` を残します。

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。 LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。 LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。 既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* DB ファイルを作成します。

## <a name="add-code-to-initialize-the-db-with-test-data"></a>テスト データで DB を初期化するコードを追加する

EF Core によって空の DB が作成されます。 このセクションでは、それにテスト データを入力するために `Initialize` メソッドを記述します。

*Data* フォルダーで、*DbInitializer.cs* という名前の新しいクラス ファイルを作成し、次のコードを追加します。

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

メモ:上のコードでは、名前空間 (`namespace ContosoUniversity.Models`) に `Data` ではなく `Models` を使用しています。 `Models` はスキャフォールディング機能によって生成されたコードと一致します。 詳細については、[こちらの GitHub のスキャフォールディングの問題](https://github.com/aspnet/Scaffolding/issues/822)に関するページを参照してください。

このコードは、DB に学生が存在するかどうかを確認します。 DB に学生が存在しない場合、テスト データを使用して DB が初期化されます。 `List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。

`EnsureCreated` メソッドは、DB のコンテキストに合わせて DB を自動的に作成します。 DB が存在する場合、`EnsureCreated` は DB を変更することなく戻ってきます。

*Program.cs* で、`Initialize` を呼び出すように `Main` メソッドを変更します。

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

学生のレコードをすべて削除し、アプリを再起動します。 DB が初期化されていない場合は、ブレーク ポイントを `Initialize` に設定して問題を診断します。

## <a name="view-the-db"></a>DB を表示する

前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。 したがって、データベース名は "SchoolContext-{GUID}" になります。 GUID は、ユーザーごとに異なります。
Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。
SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** をクリックします。

**[Tables]\(テーブル\)** ノードを展開します。

**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。

## <a name="asynchronous-code"></a>非同期コード

ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。

Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。 その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。 同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。 非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。 結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。

非同期コードが実行時に発生させるオーバーヘッドは少量です。 トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。

次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* キーワード `async` は次のことをコンパイラに指示します。
  * メソッド本文の一部にコールバックを生成する。
  * 返された [Task](/dotnet/api/system.threading.tasks.task) オブジェクトを自動作成する。 詳細については、[Task の戻り値の型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)に関する記事を参照してください。

* 暗黙の戻り値の型 `Task` は進行中の作業を表します。
* キーワード `await` により、コンパイラはメソッドを 2 つに分割します。 最初の部分は、非同期で開始される操作で終わります。 2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。
* `ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。

EF Core を利用する非同期コードの記述で注意すべき点:

* クエリやコマンドを DB に送信するステートメントのみが非同期で実行されます。 これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。 `var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。
* EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。
* 非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。

.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。

次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) の操作について説明します。



## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [次へ](xref:data/ef-rp/crud)

::: moniker-end
