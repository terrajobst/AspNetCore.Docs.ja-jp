---
title: ASP.NET Core での Entity Framework Core を使用した Razor ページ - チュートリアル 1/8
author: tdykstra
description: Entity Framework Core を使用して Razor ページ アプリを作成する方法について説明します
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/22/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 107b348b4484301b86eeb5528833914fe4c1eaf7
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080937"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="6b263-103">ASP.NET Core での Entity Framework Core を使用した Razor ページ - チュートリアル 1/8</span><span class="sxs-lookup"><span data-stu-id="6b263-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="6b263-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b263-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b263-105">これは、ASP.NET Core Razor Pages アプリで Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="6b263-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an ASP.NET Core Razor Pages app.</span></span> <span data-ttu-id="6b263-106">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="6b263-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6b263-107">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span>

[<span data-ttu-id="6b263-108">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-108">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="6b263-109">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6b263-109">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b263-110">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6b263-110">Prerequisites</span></span>

* <span data-ttu-id="6b263-111">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6b263-111">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="6b263-114">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="6b263-114">Database engines</span></span>

<span data-ttu-id="6b263-115">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="6b263-115">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="6b263-116">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-116">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="6b263-117">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="6b263-117">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6b263-118">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6b263-118">Troubleshooting</span></span>

<span data-ttu-id="6b263-119">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="6b263-119">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="6b263-120">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6b263-120">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="6b263-121">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="6b263-121">The sample app</span></span>

<span data-ttu-id="6b263-122">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="6b263-122">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="6b263-123">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b263-123">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6b263-124">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6b263-124">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="6b263-127">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="6b263-127">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="6b263-128">このチュートリアルでは、UI をカスタマイズする方法ではなく、主に EF Core の使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6b263-128">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="6b263-129">ページの上部にあるリンクを使用して、完成したプロジェクトのソース コードを取得します。</span><span class="sxs-lookup"><span data-stu-id="6b263-129">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="6b263-130">*cu30* フォルダーには、チュートリアルの ASP.NET Core 3.0 バージョン用のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b263-130">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="6b263-131">チュートリアル 1-7 のコードの状態を反映するファイルは、*cu30snapshots* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="6b263-131">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b263-133">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="6b263-133">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="6b263-134">名前に *SQLite* がある 3 つのファイルと 1 つのフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b263-134">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="6b263-135">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="6b263-135">Build the project.</span></span>
* <span data-ttu-id="6b263-136">パッケージ マネージャー コンソール (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="6b263-137">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="6b263-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b263-139">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="6b263-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="6b263-140">*ContosoUniversity.csproj* を削除し、*ContosoUniversitySQLite.csproj* の名前を *ContosoUniversity.csproj* に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="6b263-141">*Startup.cs* を削除し、*StartupSQLite.cs* の名前を *Startup.cs* に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="6b263-142">*appSettings.json* を削除し、*appSettingsSQLite.json* の名前を *appSettings.json* に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="6b263-143">*Migrations* フォルダーを削除し、*MigrationsSQL* の名前を *Migrations* に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="6b263-144">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="6b263-144">Build the project.</span></span>
* <span data-ttu-id="6b263-145">プロジェクト フォルダーのコマンド プロンプトで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-145">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="6b263-146">SQLite ツールで、次の SQL ステートメントを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-146">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="6b263-147">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="6b263-147">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="6b263-148">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="6b263-148">Create the web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b263-150">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-150">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6b263-151">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-151">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="6b263-152">プロジェクトに *ContosoUniversity* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="6b263-152">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="6b263-153">コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="6b263-153">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="6b263-154">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-154">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b263-156">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="6b263-156">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="6b263-157">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-157">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="6b263-158">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="6b263-158">Set up the site style</span></span>

<span data-ttu-id="6b263-159">*Pages/Shared/_Layout.cshtml* を更新して、サイト ヘッダー、フッター、およびメニューを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b263-159">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="6b263-160">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-160">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="6b263-161">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="6b263-161">There are three occurrences.</span></span>

* <span data-ttu-id="6b263-162">**Home** メニューと **Privacy** メニューのエントリを削除し、**About**、**Students**、**Courses**、**Instructors**、**Departments** のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b263-162">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="6b263-163">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-163">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="6b263-164">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET Core に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-164">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="6b263-165">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-165">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="6b263-166">データ モデル</span><span class="sxs-lookup"><span data-stu-id="6b263-166">The data model</span></span>

<span data-ttu-id="6b263-167">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-167">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="6b263-169">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-169">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="6b263-170">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="6b263-170">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="6b263-172">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-172">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="6b263-173">次のコードを使用して、*Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-173">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="6b263-174">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-174">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="6b263-175">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="6b263-175">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="6b263-176">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-176">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="6b263-177">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="6b263-177">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6b263-178">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-178">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="6b263-179">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-179">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="6b263-180">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-180">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="6b263-181">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="6b263-181">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="6b263-182">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="6b263-182">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="6b263-183">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-183">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="6b263-184">StudentID は、Enrollment テーブルの*外部キー*です。</span><span class="sxs-lookup"><span data-stu-id="6b263-184">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="6b263-185">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-185">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="6b263-186">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-186">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="6b263-187">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-187">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="6b263-188">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="6b263-188">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="6b263-190">以下のコードを使用して、*Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-190">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="6b263-191">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-191">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="6b263-192">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-192">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="6b263-193">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-193">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="6b263-194">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-194">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="6b263-195">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6b263-196">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="6b263-197">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="6b263-197">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6b263-198">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6b263-199">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-199">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="6b263-200">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6b263-201">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="6b263-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6b263-202">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="6b263-202">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="6b263-203">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="6b263-203">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="6b263-204">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="6b263-204">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="6b263-205">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="6b263-205">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="6b263-206">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="6b263-206">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="6b263-208">以下のコードを使用して、*Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-208">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="6b263-209">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="6b263-209">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6b263-210">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="6b263-210">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6b263-211">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="6b263-211">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="6b263-212">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="6b263-212">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="6b263-213">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6b263-213">Scaffold Student pages</span></span>

<span data-ttu-id="6b263-214">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-214">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="6b263-215">EF Core *コンテキスト* クラス。</span><span class="sxs-lookup"><span data-stu-id="6b263-215">An EF Core *context* class.</span></span> <span data-ttu-id="6b263-216">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="6b263-216">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="6b263-217">これは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="6b263-217">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="6b263-218">`Student` エンティティの CRUD (Create, Read, Update, and Delete/作成、読み取り、更新と削除) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="6b263-218">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b263-220">*Pages* フォルダー内に *Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-220">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="6b263-221">**ソリューション エクスプローラー**で、 *[Pages/Students]* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-221">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6b263-222">**[スキャフォールディングを追加]** ダイアログで、 **[Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用する Razor Pages (CRUD)\)** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-222">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="6b263-223">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="6b263-223">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="6b263-224">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-224">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="6b263-225">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-225">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="6b263-226">データ コンテキスト名を *ContosoUniversity.Models.ContosoUniversityContext* から *ContosoUniversity.Data.SchoolContext* に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-226">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="6b263-227">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="6b263-227">Select **Add**.</span></span>

<span data-ttu-id="6b263-228">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6b263-228">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b263-230">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6b263-230">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools --version 3.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
  dotnet add package Microsoft.Extensions.Logging.Debug --version 3.0.0-*
  ```

  <span data-ttu-id="6b263-231">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b263-231">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="6b263-232">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b263-232">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="6b263-233">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-233">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="6b263-234">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6b263-234">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
  ```

* <span data-ttu-id="6b263-235">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="6b263-235">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="6b263-236">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="6b263-236">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="6b263-237">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="6b263-237">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="6b263-238">前の手順で問題が発生した場合は、プロジェクトをビルドし、スキャフォールディング ステップを再試行してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-238">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="6b263-239">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6b263-239">The scaffolding process:</span></span>

* <span data-ttu-id="6b263-240">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-240">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="6b263-241">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6b263-241">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="6b263-242">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6b263-242">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="6b263-243">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6b263-243">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="6b263-244">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6b263-244">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="6b263-245">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6b263-245">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="6b263-246">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-246">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="6b263-247">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b263-247">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="6b263-248">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="6b263-248">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="6b263-249">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="6b263-249">Database connection string</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b263-251">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-251">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="6b263-252">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="6b263-252">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="6b263-253">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-253">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b263-255">*CU.db* という名前の SQLite データベース ファイルを指すように接続文字列を変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-255">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="6b263-256">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="6b263-256">Update the database context class</span></span>

<span data-ttu-id="6b263-257">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="6b263-257">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="6b263-258">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="6b263-258">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6b263-259">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-259">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="6b263-260">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="6b263-260">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6b263-261">次のコードを使用して *SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="6b263-261">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="6b263-262">強調表示されたコードで、各エンティティ セットの [DbSet\< TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-262">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="6b263-263">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="6b263-263">In EF Core terminology:</span></span>

* <span data-ttu-id="6b263-264">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="6b263-264">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="6b263-265">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="6b263-265">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6b263-266">エンティティ セットには複数のエンティティが含まれているため、DBSet プロパティは複数形の名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b263-266">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="6b263-267">スキャフォールディング ツールによって `Student` DBSet を作成したので、このステップでこれを複数形の `Students` に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-267">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="6b263-268">Razor Pages コードを新しい DBSet 名と一致させるには、プロジェクト全体で `_context.Student` を `_context.Students` にグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-268">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="6b263-269">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="6b263-269">There are 8 occurrences.</span></span>

<span data-ttu-id="6b263-270">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-270">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="6b263-271">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="6b263-271">Startup.cs</span></span>

<span data-ttu-id="6b263-272">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="6b263-272">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6b263-273">サービス (EF Core データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-273">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6b263-274">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-274">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6b263-275">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-275">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6b263-276">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="6b263-276">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-277">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b263-278">`ConfigureServices` では、強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="6b263-278">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b263-280">`ConfigureServices` では、スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-280">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="6b263-281">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-281">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6b263-282">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="6b263-282">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="6b263-283">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="6b263-283">Create the database</span></span>

<span data-ttu-id="6b263-284">データベースが存在しない場合は、*Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-284">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="6b263-285">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="6b263-285">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="6b263-286">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-286">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="6b263-287">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-287">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="6b263-288">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b263-288">Delete the database.</span></span> <span data-ttu-id="6b263-289">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="6b263-289">Any existing data is lost.</span></span>
* <span data-ttu-id="6b263-290">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-290">Change the data model.</span></span> <span data-ttu-id="6b263-291">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b263-291">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="6b263-292">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-292">Run the app.</span></span>
* <span data-ttu-id="6b263-293">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-293">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="6b263-294">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="6b263-294">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="6b263-295">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="6b263-295">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="6b263-296">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-296">When that is the case, use migrations.</span></span>

<span data-ttu-id="6b263-297">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-297">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="6b263-298">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="6b263-298">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="6b263-299">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="6b263-299">Test the app</span></span>

* <span data-ttu-id="6b263-300">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-300">Run the app.</span></span>
* <span data-ttu-id="6b263-301">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-301">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="6b263-302">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="6b263-302">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="6b263-303">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="6b263-303">Seed the database</span></span>

<span data-ttu-id="6b263-304">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-304">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="6b263-305">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b263-305">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="6b263-306">次のコードを使用して、*Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-306">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="6b263-307">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-307">The code checks if there are any students in the database.</span></span> <span data-ttu-id="6b263-308">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-308">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="6b263-309">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-309">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="6b263-310">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6b263-310">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b263-312">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-312">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b263-314">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b263-314">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="6b263-315">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="6b263-315">Restart the app.</span></span>

* <span data-ttu-id="6b263-316">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-316">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="6b263-317">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="6b263-317">View the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-318">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-318">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b263-319">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="6b263-319">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="6b263-320">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-320">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="6b263-321">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-321">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="6b263-322">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6b263-322">Expand the **Tables** node.</span></span>
* <span data-ttu-id="6b263-323">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-323">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="6b263-324">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-324">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b263-326">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-326">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="6b263-327">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="6b263-327">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="6b263-328">非同期コード</span><span class="sxs-lookup"><span data-stu-id="6b263-328">Asynchronous code</span></span>

<span data-ttu-id="6b263-329">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="6b263-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6b263-330">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6b263-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6b263-331">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="6b263-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6b263-332">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="6b263-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6b263-333">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6b263-334">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="6b263-335">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="6b263-335">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="6b263-336">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-336">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6b263-337">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-337">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="6b263-338">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-338">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="6b263-339">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="6b263-339">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="6b263-340">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="6b263-340">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="6b263-341">戻り値の型 `Task<T>` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="6b263-341">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="6b263-342">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="6b263-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6b263-343">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="6b263-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6b263-344">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="6b263-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="6b263-345">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="6b263-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6b263-346">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="6b263-346">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="6b263-347">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="6b263-348">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-348">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6b263-349">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="6b263-349">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="6b263-350">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="6b263-350">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="6b263-351">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-351">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="6b263-352">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-352">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="6b263-353">次の手順</span><span class="sxs-lookup"><span data-stu-id="6b263-353">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6b263-354">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="6b263-354">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b263-355">Contoso University のサンプル Web アプリでは、Entity Framework (EF) Core を使用して ASP.NET Core Razor Pages アプリを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6b263-355">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="6b263-356">このサンプル アプリは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="6b263-356">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6b263-357">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b263-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="6b263-358">このページは、Contoso University のサンプル アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="6b263-358">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="6b263-359">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-359">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="6b263-360">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6b263-360">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b263-361">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6b263-361">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-362">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-362">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-363">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-363">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="6b263-364">[Razor ページ](xref:razor-pages/index)に関する知識。</span><span class="sxs-lookup"><span data-stu-id="6b263-364">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="6b263-365">Razor ページのプログラミングが初めての場合、このシリーズを始める前に[こちらの入門編](xref:tutorials/razor-pages/razor-pages-start)を完了してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-365">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6b263-366">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6b263-366">Troubleshooting</span></span>

<span data-ttu-id="6b263-367">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="6b263-367">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="6b263-368">ヘルプが必要なときは、[StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) について質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6b263-368">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="6b263-369">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="6b263-369">The Contoso University web app</span></span>

<span data-ttu-id="6b263-370">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="6b263-370">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="6b263-371">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b263-371">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6b263-372">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6b263-372">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

<span data-ttu-id="6b263-375">このサイトの UI スタイルは、組み込みのテンプレートによって生成されるものに類似しています。</span><span class="sxs-lookup"><span data-stu-id="6b263-375">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="6b263-376">このチュートリアルでは、UI ではなく、EF Core と Razor ページに重点を置きます。</span><span class="sxs-lookup"><span data-stu-id="6b263-376">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="6b263-377">ContosoUniversity Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="6b263-377">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-378">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b263-379">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-379">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6b263-380">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-380">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="6b263-381">プロジェクトに **ContosoUniversity** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="6b263-381">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="6b263-382">このプロジェクトに *ContosoUniversity* という名前を付けることは重要です。そうすることでコードをコピーしたり貼り付けるときに名前空間が一致します。</span><span class="sxs-lookup"><span data-stu-id="6b263-382">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="6b263-383">ドロップダウン リストで **[ASP.NET Core 2.1]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-383">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="6b263-384">前の手順の画像については、「[Razor Web アプリの作成](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-384">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="6b263-385">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-385">Run the app.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="6b263-387">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="6b263-387">Set up the site style</span></span>

<span data-ttu-id="6b263-388">変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="6b263-388">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="6b263-389">*Pages/Shared/_Layout.cshtml* に次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="6b263-389">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="6b263-390">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-390">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="6b263-391">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="6b263-391">There are three occurrences.</span></span>

* <span data-ttu-id="6b263-392">メニュー エントリとして「**Students**」、「**Courses**」、「**Instructors**」、「**Departments**」を追加し、「**Contact**」を削除します。</span><span class="sxs-lookup"><span data-stu-id="6b263-392">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="6b263-393">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-393">The changes are highlighted.</span></span> <span data-ttu-id="6b263-394">(マークアップは一部表示されて*いません*。)</span><span class="sxs-lookup"><span data-stu-id="6b263-394">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="6b263-395">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-395">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="6b263-396">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="6b263-396">Create the data model</span></span>

<span data-ttu-id="6b263-397">Contoso University アプリのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-397">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="6b263-398">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="6b263-398">Start with the following three entities:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="6b263-400">`Student` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="6b263-400">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="6b263-401">`Course` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="6b263-401">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="6b263-402">1 人の学生をさまざまな講座に登録できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-402">A student can enroll in any number of courses.</span></span> <span data-ttu-id="6b263-403">1 つの講座に任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-403">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="6b263-404">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-404">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="6b263-405">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="6b263-405">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="6b263-407">*Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-407">Create a *Models* folder.</span></span> <span data-ttu-id="6b263-408">以下のコードを使用して、*Models* フォルダーで、*Student.cs* という名前のクラス ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-408">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="6b263-409">`ID` プロパティは、このクラスに相当するデータベース (DB) テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-409">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="6b263-410">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="6b263-410">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="6b263-411">`classnameID` の `classname` はクラスの名前です。</span><span class="sxs-lookup"><span data-stu-id="6b263-411">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="6b263-412">代わりの自動的に認識される主キーは、前の例の `StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-412">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="6b263-413">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="6b263-413">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6b263-414">ナビゲーション プロパティは、このエンティティに関連する他のエンティティにリンクします。</span><span class="sxs-lookup"><span data-stu-id="6b263-414">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="6b263-415">この例では、`Student entity` の `Enrollments` プロパティによって、その `Student` に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-415">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="6b263-416">たとえば、DB 内のある Student 行に 2 つ関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-416">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="6b263-417">関連する `Enrollment` 行とは、その学生の主キー値を `StudentID` 列に含む行です。</span><span class="sxs-lookup"><span data-stu-id="6b263-417">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="6b263-418">たとえば、ID=1 の学生には、`Enrollment` テーブルに行が 2 つあるとします。</span><span class="sxs-lookup"><span data-stu-id="6b263-418">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="6b263-419">その `Enrollment` テーブルには、`StudentID` = 1 の行が 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="6b263-419">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="6b263-420">`StudentID` は `Enrollment` テーブルの外部キーであり、`Student` テーブルでその学生を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b263-420">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="6b263-421">あるナビゲーション プロパティが複数のエンティティを保持できる場合、そのナビゲーション プロパティは `ICollection<T>` のようなリスト型にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b263-421">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="6b263-422">`ICollection<T>` を指定するか、`List<T>` や `HashSet<T>` のような型を指定できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-422">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="6b263-423">`ICollection<T>` を使用する場合、EF Core で `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-423">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="6b263-424">複数のエンティティを保持するナビゲーション プロパティは、多対多または一対多の関係から発生します。</span><span class="sxs-lookup"><span data-stu-id="6b263-424">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="6b263-425">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="6b263-425">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="6b263-427">以下のコードを使用して、*Models* フォルダーで、*Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-427">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="6b263-428">`EnrollmentID` プロパティは主キーです。</span><span class="sxs-lookup"><span data-stu-id="6b263-428">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="6b263-429">このエンティティでは、`Student` エンティティのような `ID` ではなく、`classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-429">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="6b263-430">一般的に、開発者は 1 つのパターンを選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="6b263-430">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="6b263-431">後のチュートリアルでは、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装します。</span><span class="sxs-lookup"><span data-stu-id="6b263-431">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="6b263-432">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-432">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6b263-433">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-433">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="6b263-434">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="6b263-434">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6b263-435">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-435">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6b263-436">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-436">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="6b263-437">`Student` エンティティは、複数の `Enrollment` エンティティが含まれる `Student.Enrollments` ナビゲーション プロパティとは異なります。</span><span class="sxs-lookup"><span data-stu-id="6b263-437">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="6b263-438">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="6b263-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6b263-439">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="6b263-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6b263-440">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="6b263-440">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="6b263-441">たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーは `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="6b263-441">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="6b263-442">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="6b263-442">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="6b263-443">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="6b263-443">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="6b263-444">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="6b263-444">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="6b263-446">以下のコードを使用して、*Models* フォルダーで、*Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-446">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="6b263-447">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="6b263-447">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6b263-448">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="6b263-448">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6b263-449">`DatabaseGenerated` 属性によって、アプリで主キーを指定できます。DB に生成させるのではありません。</span><span class="sxs-lookup"><span data-stu-id="6b263-449">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="6b263-450">Student モデルをスキャホールディングする</span><span class="sxs-lookup"><span data-stu-id="6b263-450">Scaffold the student model</span></span>

<span data-ttu-id="6b263-451">このセクションでは、Student モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="6b263-451">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="6b263-452">つまり、スキャフォールディング ツールにより、Student モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-452">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="6b263-453">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="6b263-453">Build the project.</span></span>
* <span data-ttu-id="6b263-454">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-454">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-455">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-455">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b263-456">**ソリューション エクスプローラー**で、 *[Pages/Students]* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-456">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6b263-457">**[スキャフォールディングを追加]** ダイアログで、 **[Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用する Razor Pages (CRUD)\)** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-457">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="6b263-458">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="6b263-458">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="6b263-459">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-459">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="6b263-460">**[データ コンテキスト クラス]** 行で **+** (+) 記号を選択し、生成された名前を **ContosoUniversity.Models.SchoolContext** に変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-460">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="6b263-461">**[データ コンテキスト クラス]** ドロップダウンで、 **[ContosoUniversity.Models.SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-461">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="6b263-462">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="6b263-462">Select **Add**.</span></span>

![CRUD ダイアログ](intro/_static/s1.png)

<span data-ttu-id="6b263-464">前の手順に問題がある場合は、「[ムービー モデルのスキャフォールディング](xref:tutorials/razor-pages/model#scaffold-the-movie-model)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-464">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-465">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-465">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b263-466">次のコマンドを実行して、Student モデルをスキャホールディングします。</span><span class="sxs-lookup"><span data-stu-id="6b263-466">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="6b263-467">スキャフォールディングのプロセスが作成され、次のファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-467">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="6b263-468">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="6b263-468">Files created</span></span>

* <span data-ttu-id="6b263-469">*Pages/Students* 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="6b263-469">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="6b263-470">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="6b263-470">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="6b263-471">ファイルの更新</span><span class="sxs-lookup"><span data-stu-id="6b263-471">File updates</span></span>

* <span data-ttu-id="6b263-472">*Startup.cs*:このファイルに対する変更を次のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="6b263-472">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="6b263-473">*appsettings.json*:ローカル データベースへの接続に使用される接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b263-473">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6b263-474">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="6b263-474">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6b263-475">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="6b263-475">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6b263-476">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-476">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6b263-477">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-477">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6b263-478">db コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-478">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6b263-479">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="6b263-479">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="6b263-480">*Startup.cs* の `ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="6b263-480">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="6b263-481">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="6b263-481">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="6b263-482">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-482">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6b263-483">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="6b263-483">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="6b263-484">main を更新する</span><span class="sxs-lookup"><span data-stu-id="6b263-484">Update main</span></span>

<span data-ttu-id="6b263-485">*Program.cs* で、次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-485">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="6b263-486">依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="6b263-486">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="6b263-487">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6b263-487">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="6b263-488">`EnsureCreated` メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="6b263-488">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="6b263-489">次は、更新された *Program.cs* ファイルのコードです。</span><span class="sxs-lookup"><span data-stu-id="6b263-489">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="6b263-490">`EnsureCreated` で、コンテキストのデータベースが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-490">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="6b263-491">存在する場合、アクションは何も実行されません。</span><span class="sxs-lookup"><span data-stu-id="6b263-491">If it exists, no action is taken.</span></span> <span data-ttu-id="6b263-492">存在しない場合は、データベースとそのすべてのスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-492">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="6b263-493">`EnsureCreated` は、データベースの作成に移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="6b263-493">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="6b263-494">`EnsureCreated` で作成されたデータベースは、後で移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="6b263-494">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="6b263-495">アプリの起動時に `EnsureCreated` が呼び出され、次のワークフローが可能になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-495">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="6b263-496">DB を削除します。</span><span class="sxs-lookup"><span data-stu-id="6b263-496">Delete the DB.</span></span>
* <span data-ttu-id="6b263-497">DB スキーマを変更します (たとえば、`EmailAddress` フィールドを追加します)。</span><span class="sxs-lookup"><span data-stu-id="6b263-497">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="6b263-498">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-498">Run the app.</span></span>
* <span data-ttu-id="6b263-499">`EnsureCreated` によって、`EmailAddress` 列を含む DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-499">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="6b263-500">`EnsureCreated` は、スキーマが急速に進化している開発の早期に便利です。</span><span class="sxs-lookup"><span data-stu-id="6b263-500">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="6b263-501">このチュートリアルの後半では、DB は削除され、移行が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-501">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="6b263-502">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="6b263-502">Test the app</span></span>

<span data-ttu-id="6b263-503">アプリを実行し、Cookie ポリシーに同意します。</span><span class="sxs-lookup"><span data-stu-id="6b263-503">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="6b263-504">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="6b263-504">This app doesn't keep personal information.</span></span> <span data-ttu-id="6b263-505">Cookie ポリシーについては、[EU の一般的なデータ保護規制 (GDPR) のサポート](xref:security/gdpr)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-505">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="6b263-506">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6b263-506">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="6b263-507">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="6b263-507">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="6b263-508">SchoolContext DB コンテキストを確認する</span><span class="sxs-lookup"><span data-stu-id="6b263-508">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="6b263-509">所与のデータ モデルの EF Core 機能を調整するメイン クラスは、DB コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="6b263-509">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="6b263-510">データ コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-510">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6b263-511">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-511">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="6b263-512">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="6b263-512">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6b263-513">次のコードを使用して *SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="6b263-513">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="6b263-514">強調表示されたコードで、各エンティティ セットの [DbSet\< TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-514">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="6b263-515">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="6b263-515">In EF Core terminology:</span></span>

* <span data-ttu-id="6b263-516">エンティティ セットは通常、DB テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="6b263-516">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="6b263-517">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="6b263-517">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6b263-518">`DbSet<Enrollment>` と `DbSet<Course>` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="6b263-518">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="6b263-519">EF Core にはそれらが暗黙的に含まれます。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="6b263-519">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="6b263-520">このチュートリアルでは、`SchoolContext` に `DbSet<Enrollment>` と `DbSet<Course>` を残します。</span><span class="sxs-lookup"><span data-stu-id="6b263-520">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="6b263-521">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6b263-521">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6b263-522">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-522">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="6b263-523">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="6b263-523">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="6b263-524">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="6b263-524">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6b263-525">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* DB ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-525">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="6b263-526">テスト データで DB を初期化するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="6b263-526">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="6b263-527">EF Core によって空の DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-527">EF Core creates an empty DB.</span></span> <span data-ttu-id="6b263-528">このセクションでは、それにテスト データを入力するために `Initialize` メソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="6b263-528">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="6b263-529">*Data* フォルダーで、*DbInitializer.cs* という名前の新しいクラス ファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b263-529">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="6b263-530">メモ:上のコードでは、名前空間 (`namespace ContosoUniversity.Models`) に `Data` ではなく `Models` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="6b263-530">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="6b263-531">`Models` はスキャフォールディング機能によって生成されたコードと一致します。</span><span class="sxs-lookup"><span data-stu-id="6b263-531">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="6b263-532">詳細については、[こちらの GitHub のスキャフォールディングの問題](https://github.com/aspnet/Scaffolding/issues/822)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-532">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="6b263-533">このコードは、DB に学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-533">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="6b263-534">DB に学生が存在しない場合、テスト データを使用して DB が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-534">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="6b263-535">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="6b263-535">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="6b263-536">`EnsureCreated` メソッドは、DB のコンテキストに合わせて DB を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="6b263-536">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="6b263-537">DB が存在する場合、`EnsureCreated` は DB を変更することなく戻ってきます。</span><span class="sxs-lookup"><span data-stu-id="6b263-537">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="6b263-538">*Program.cs* で、`Initialize` を呼び出すように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="6b263-538">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6b263-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b263-539">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b263-540">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b263-540">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6b263-541">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b263-541">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b263-542">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b263-542">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="6b263-543">DB を表示する</span><span class="sxs-lookup"><span data-stu-id="6b263-543">View the DB</span></span>

<span data-ttu-id="6b263-544">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-544">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="6b263-545">したがって、データベース名は "SchoolContext-{GUID}" になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-545">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="6b263-546">GUID は、ユーザーごとに異なります。</span><span class="sxs-lookup"><span data-stu-id="6b263-546">The GUID will be different for each user.</span></span>
<span data-ttu-id="6b263-547">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="6b263-547">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="6b263-548">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6b263-548">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="6b263-549">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6b263-549">Expand the **Tables** node.</span></span>

<span data-ttu-id="6b263-550">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-550">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="6b263-551">非同期コード</span><span class="sxs-lookup"><span data-stu-id="6b263-551">Asynchronous code</span></span>

<span data-ttu-id="6b263-552">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="6b263-552">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6b263-553">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6b263-553">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6b263-554">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="6b263-554">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6b263-555">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="6b263-555">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6b263-556">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-556">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6b263-557">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="6b263-557">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="6b263-558">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="6b263-558">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="6b263-559">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-559">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6b263-560">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="6b263-560">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="6b263-561">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="6b263-561">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="6b263-562">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="6b263-562">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="6b263-563">返された [Task](/dotnet/api/system.threading.tasks.task) オブジェクトを自動作成する。</span><span class="sxs-lookup"><span data-stu-id="6b263-563">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="6b263-564">詳細については、[Task の戻り値の型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-564">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="6b263-565">暗黙の戻り値の型 `Task` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="6b263-565">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="6b263-566">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="6b263-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6b263-567">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="6b263-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6b263-568">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="6b263-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="6b263-569">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="6b263-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6b263-570">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="6b263-570">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="6b263-571">クエリやコマンドを DB に送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="6b263-571">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="6b263-572">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b263-572">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6b263-573">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="6b263-573">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="6b263-574">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="6b263-574">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="6b263-575">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b263-575">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="6b263-576">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b263-576">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="6b263-577">次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) の操作について説明します。</span><span class="sxs-lookup"><span data-stu-id="6b263-577">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="6b263-578">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6b263-578">Additional resources</span></span>

* [<span data-ttu-id="6b263-579">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="6b263-579">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="6b263-580">次へ</span><span class="sxs-lookup"><span data-stu-id="6b263-580">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
