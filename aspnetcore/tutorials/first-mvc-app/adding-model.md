---
title: ASP.NET Core MVC アプリへのモデルの追加
author: rick-anderson
description: 単純な ASP.NET Core アプリケーションにモデルを追加します。
ms.author: riande
ms.date: 8/15/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 5d4251a2577111324aa2cfb715c41e3ecad5a9d1
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722799"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="976aa-103">ASP.NET Core MVC アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="976aa-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="976aa-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="976aa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="976aa-105">このセクションでは、データベースのムービーを管理するクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="976aa-106">これらのクラスは、**M**VC アプリの "**モ**デル" 部分です。</span><span class="sxs-lookup"><span data-stu-id="976aa-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="976aa-107">[Entity Framework Core](/ef/core) (EF Core) でこれらのクラスを使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="976aa-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="976aa-108">EF Core は、記述する必要があるデータ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="976aa-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="976aa-109">作成するモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (**P**lain-**O**ld **C**LR **O**bjects から) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="976aa-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="976aa-110">これらは単に、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="976aa-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="976aa-111">このチュートリアルでは、まずモデル クラスを記述し、EF コアによってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="976aa-112">ここで取り上げていない別の方法は、既存のデータベースからモデル クラスを生成する方法です。</span><span class="sxs-lookup"><span data-stu-id="976aa-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="976aa-113">その方法については、[ASP.NET Core の既存のデータベース](/ef/core/get-started/aspnetcore/existing-db)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="976aa-114">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="976aa-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-116">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="976aa-117">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="976aa-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="976aa-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="976aa-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="976aa-119">*Movie.cs* という名前のファイルを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="976aa-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="976aa-121">*Models* フォルダーを右クリックし、 **[追加]**  >  **[新しいクラス]**  >  **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-121">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="976aa-122">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="976aa-122">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="976aa-123">次のコードで *Movie.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="976aa-123">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="976aa-124">`Movie` クラスには、データベースで主キー用に必要となる `Id` フィールドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="976aa-124">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="976aa-125">`ReleaseDate` の [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性により、データの型 (`Date`) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-125">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="976aa-126">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="976aa-126">With this attribute:</span></span>

  * <span data-ttu-id="976aa-127">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="976aa-127">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="976aa-128">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="976aa-128">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="976aa-129">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-129">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="976aa-130">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="976aa-130">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-132">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-132">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="976aa-134">PMC で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="976aa-134">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="976aa-135">上記のコマンドによって EF Core SQL Server プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-135">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="976aa-136">プロバイダー パッケージによって、依存関係として EF Core パッケージがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="976aa-136">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="976aa-137">追加のパッケージは、このチュートリアルの後半で、スキャフォールディングの手順で自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="976aa-137">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="976aa-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="976aa-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="976aa-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="976aa-140">**[プロジェクト]** メニューから、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-140">From the **Project** menu, select **Manage Nuget Packages**.</span></span>

<span data-ttu-id="976aa-141">右上の **[検索]** フィールドに「`Microsoft.EntityFrameworkCore.SQLite`」と入力し、**Return** キーを押して検索します。</span><span class="sxs-lookup"><span data-stu-id="976aa-141">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="976aa-142">一致する NuGet パッケージを選択し、 **[パッケージの追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="976aa-142">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet パッケージを追加する](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="976aa-144">**[プロジェクトの選択]** ダイアログが、`MvcMovie` プロジェクトが選択された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-144">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="976aa-145">**[OK]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="976aa-145">Press the **Ok** button.</span></span>

<span data-ttu-id="976aa-146">**[ライセンスの同意]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-146">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="976aa-147">必要に応じてライセンスを確認し、 **[同意する]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="976aa-147">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="976aa-148">上記の手順を繰り返して、次の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="976aa-148">Repeat the above steps to install the following NuGet packages:</span></span>
 * `Microsoft.VisualStudio.Web.CodeGeneration.Design`
 * `Microsoft.EntityFrameworkCore.SqlServer`
 * `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="976aa-149">データベース コンテキスト クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="976aa-149">Create a database context class</span></span>

<span data-ttu-id="976aa-150">データベース コンテキスト クラスは、`Movie` モデルの EF Core 機能 (作成、読み取り、更新、削除) を調整するために必要です。</span><span class="sxs-lookup"><span data-stu-id="976aa-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="976aa-151">データベース コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生し、データ モデルに含めるエンティティを指定します。</span><span class="sxs-lookup"><span data-stu-id="976aa-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="976aa-152">*Data* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="976aa-152">Create a *Data* folder.</span></span>

<span data-ttu-id="976aa-153">次のコードで *Data/MvcMovieContext.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="976aa-154">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="976aa-155">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="976aa-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="976aa-156">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="976aa-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="976aa-157">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="976aa-157">Register the database context</span></span>

<span data-ttu-id="976aa-158">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="976aa-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="976aa-159">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="976aa-160">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="976aa-161">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="976aa-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="976aa-162">このセクションでは、DI コンテナーにデータベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="976aa-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="976aa-163">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="976aa-164">`Startup.ConfigureServices` で次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="976aa-167">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="976aa-168">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="976aa-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="976aa-169">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="976aa-169">Add a database connection string</span></span>

<span data-ttu-id="976aa-170">*appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-172">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="976aa-173">コンパイラ エラーのチェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="976aa-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="976aa-174">ムービー ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="976aa-174">Scaffold movie pages</span></span>

<span data-ttu-id="976aa-175">スキャフォールディング ツールを使用し、ムービー モデルの作成、読み取り、更新、削除の (CRUD) ページを生成します。</span><span class="sxs-lookup"><span data-stu-id="976aa-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-177">*ソリューション エクスプローラー*で、**Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="976aa-179">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="976aa-181">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="976aa-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="976aa-182">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="976aa-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="976aa-183">**データ コンテキスト クラス:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="976aa-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc3.png)

* <span data-ttu-id="976aa-185">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="976aa-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="976aa-186">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="976aa-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="976aa-187">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-187">Select **Add**</span></span>

<span data-ttu-id="976aa-188">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-188">Visual Studio creates:</span></span>

* <span data-ttu-id="976aa-189">ムービー コントローラー (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="976aa-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="976aa-190">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー ファイル (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="976aa-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="976aa-191">このようなファイルの自動作成は、"*スキャフォールディング*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="976aa-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="976aa-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="976aa-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="976aa-193">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="976aa-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="976aa-194">Linux で、スキャフォールディング ツールのパスをエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="976aa-194">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="976aa-195">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="976aa-195">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="976aa-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="976aa-197">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="976aa-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="976aa-198">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="976aa-198">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="976aa-199">データベースが存在しないため、スキャフォールディング ページをまだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="976aa-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="976aa-200">アプリを実行し、 **[Movie App]** リンクをクリックすると、 *[データベースを開けません]* または *[そのようなテーブルはありません:Movie*] というエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="976aa-201">最初の移行</span><span class="sxs-lookup"><span data-stu-id="976aa-201">Initial migration</span></span>

<span data-ttu-id="976aa-202">EF Core [移行](xref:data/ef-mvc/migrations)機能を使用し、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="976aa-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="976aa-203">移行は、データ モデルに合わせてデータベースを作成したり、更新したりできる一連のツールです。</span><span class="sxs-lookup"><span data-stu-id="976aa-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-205">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="976aa-206">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="976aa-206">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="976aa-207">`Add-Migration InitialCreate`:*Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-207">`Add-Migration InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="976aa-208">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="976aa-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="976aa-209">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="976aa-210">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="976aa-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="976aa-211">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="976aa-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="976aa-212">`Update-Database`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="976aa-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="976aa-213">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="976aa-214">データベース更新コマンドにより、次の警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="976aa-215">エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="976aa-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="976aa-216">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="976aa-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="976aa-217">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="976aa-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="976aa-218">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-219">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="976aa-220">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="976aa-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="976aa-221">`ef migrations add InitialCreate`:*Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="976aa-222">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="976aa-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="976aa-223">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="976aa-224">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="976aa-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="976aa-225">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます (*Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="976aa-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="976aa-226">`ef database update`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="976aa-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="976aa-227">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [ more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="976aa-228">InitialCreate クラス</span><span class="sxs-lookup"><span data-stu-id="976aa-228">The InitialCreate class</span></span>

<span data-ttu-id="976aa-229">*Migrations/{timestamp}_InitialCreate.cs* 移行ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="976aa-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

 <span data-ttu-id="976aa-230">`Up` メソッドにより Movie テーブルが作成され、主キーとして `Id` が構成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="976aa-231">`Down` メソッドにより、`Up` 移行で行われたスキーマ変更が元に戻ります。</span><span class="sxs-lookup"><span data-stu-id="976aa-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="976aa-232">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="976aa-232">Test the app</span></span>

* <span data-ttu-id="976aa-233">アプリを実行し、 **[Movie App]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="976aa-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="976aa-234">次のような例外が表示された場合:</span><span class="sxs-lookup"><span data-stu-id="976aa-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="976aa-237">[移行手順](#migration)を実行しなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="976aa-238">**Create** ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="976aa-238">Test the **Create** page.</span></span> <span data-ttu-id="976aa-239">データを入力して送信します。</span><span class="sxs-lookup"><span data-stu-id="976aa-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="976aa-240">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="976aa-241">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="976aa-242">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="976aa-242">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="976aa-243">**[編集]** 、 **[詳細]** 、 **[削除]** の各ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="976aa-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="976aa-244">コントローラーの依存関係挿入</span><span class="sxs-lookup"><span data-stu-id="976aa-244">Dependency injection in the controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-246">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="976aa-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="976aa-247">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="976aa-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="976aa-248">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="976aa-250">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="976aa-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="976aa-251">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="976aa-252">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="976aa-252">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="976aa-253">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="976aa-253">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="976aa-254">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="976aa-254">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="976aa-255">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-255">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="976aa-256">この厳密に型指定された方法では、コンパイル時にコードを確認できます。</span><span class="sxs-lookup"><span data-stu-id="976aa-256">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="976aa-257">スキャフォールディング メカニズムでは、`MoviesController` のクラスとビューで、この手法 (つまり、厳密に型指定されたモデルを渡しました) が使用されました。</span><span class="sxs-lookup"><span data-stu-id="976aa-257">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="976aa-258">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="976aa-258">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="976aa-259">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-259">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="976aa-260">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="976aa-260">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="976aa-261">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="976aa-261">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="976aa-262">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="976aa-262">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="976aa-263">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="976aa-263">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="976aa-264">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="976aa-264">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="976aa-265">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-265">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="976aa-266">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-266">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="976aa-267">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-267">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="976aa-268">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-268">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="976aa-269">ビュー ファイルの一番上にある `@model` ステートメントにより、ビューで求められるオブジェクトの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-269">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="976aa-270">ムービー コントローラーが作成されたとき、次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="976aa-270">When the movie controller was created, the following `@model` statement was included:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="976aa-271">この `@model` ディレクティブにより、コントローラーでビューに渡されたムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="976aa-271">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="976aa-272">`Model` オブジェクトは厳密に型指定されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-272">The `Model` object is strongly typed.</span></span> <span data-ttu-id="976aa-273">たとえば、*Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="976aa-273">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="976aa-274">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="976aa-274">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="976aa-275">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-275">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="976aa-276">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-276">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="976aa-277">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="976aa-277">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="976aa-278">ムービー コントローラーが作成されたとき、スキャフォールディングにより、*Index.cshtml* ファイルの一番上に次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="976aa-278">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="976aa-279">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="976aa-279">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="976aa-280">たとえば、*Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="976aa-280">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="976aa-281">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-281">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="976aa-282">これには、コンパイル時にコードを確認できるなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-282">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="976aa-283">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="976aa-283">Additional resources</span></span>

* [<span data-ttu-id="976aa-284">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="976aa-284">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="976aa-285">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="976aa-285">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="976aa-286">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル SQL の使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="976aa-286">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="976aa-287">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="976aa-287">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-288">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-289">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-289">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="976aa-290">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="976aa-290">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-291">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-291">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="976aa-292">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-292">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="976aa-293">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="976aa-293">Scaffold the movie model</span></span>

<span data-ttu-id="976aa-294">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="976aa-294">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="976aa-295">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-295">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-296">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-296">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-297">*ソリューション エクスプローラー*で、**Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-297">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="976aa-299">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-299">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="976aa-301">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="976aa-301">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="976aa-302">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="976aa-302">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="976aa-303">**データ コンテキスト クラス:** **+** アイコンを選択して、既定の **MvcMovie.Models.MvcMovieContext** を追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-303">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc.png)

* <span data-ttu-id="976aa-305">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="976aa-305">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="976aa-306">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="976aa-306">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="976aa-307">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-307">Select **Add**</span></span>

![[コントローラーの追加] ダイアログ](adding-model/_static/add_controller2.png)

<span data-ttu-id="976aa-309">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-309">Visual Studio creates:</span></span>

* <span data-ttu-id="976aa-310">Entity Framework Core の[データベース コンテキスト クラス](xref:data/ef-mvc/intro#create-the-database-context)(*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="976aa-310">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="976aa-311">ムービー コントローラー (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="976aa-311">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="976aa-312">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー ファイル (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="976aa-312">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="976aa-313">データベース コンテキストと [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (作成、読み取り、更新、および削除) アクション メソッドとビューの自動作成は、*スキャフォールディング*と言います。</span><span class="sxs-lookup"><span data-stu-id="976aa-313">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="976aa-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="976aa-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="976aa-315">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="976aa-315">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="976aa-316">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="976aa-316">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="976aa-317">Linux で、スキャフォールディング ツールのパスをエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="976aa-317">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="976aa-318">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="976aa-318">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="976aa-319">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="976aa-320">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="976aa-320">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="976aa-321">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="976aa-321">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="976aa-322">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="976aa-322">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="976aa-323">アプリを実行し、 **[MVC Movie]\(MVC ムービー\)** リンクをクリックすると、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-323">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-324">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-324">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-325">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-325">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="976aa-326">データベースを作成する必要があり、それには EF Core [移行](xref:data/ef-mvc/migrations)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="976aa-326">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="976aa-327">移行では、データ モデルに一致するデータベースを作成し、データ モデルの変更時にデータベース スキーマを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="976aa-327">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="976aa-328">最初の移行</span><span class="sxs-lookup"><span data-stu-id="976aa-328">Initial migration</span></span>

<span data-ttu-id="976aa-329">このセクションでは、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="976aa-329">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="976aa-330">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="976aa-330">Add an initial migration.</span></span>
* <span data-ttu-id="976aa-331">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="976aa-331">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-332">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="976aa-333">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="976aa-333">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="976aa-335">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="976aa-335">In the PMC, enter the following commands:</span></span>

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="976aa-336">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-336">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="976aa-337">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="976aa-337">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="976aa-338">`Initial` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="976aa-338">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="976aa-339">任意の名前を使用できますが、慣例により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-339">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="976aa-340">詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-340">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="976aa-341">`Update-Database` コマンドは、データベースを作成する、*Migrations/{time-stamp}_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="976aa-341">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-342">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-342">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="976aa-343">`ef migrations add InitialCreate` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-343">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="976aa-344">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます (*Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="976aa-344">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="976aa-345">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="976aa-345">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="976aa-346">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-346">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="976aa-347">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="976aa-347">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="976aa-348">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="976aa-348">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="976aa-349">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-349">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="976aa-350">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-350">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="976aa-351">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="976aa-351">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="976aa-352">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="976aa-352">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="976aa-353">スキャフォールディング ツールが自動的に DB コンテキストを作成し、それを DI コンテナーに登録します。</span><span class="sxs-lookup"><span data-stu-id="976aa-353">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="976aa-354">次の `Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="976aa-354">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="976aa-355">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="976aa-355">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="976aa-356">`MvcMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-356">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="976aa-357">データ コンテキスト (`MvcMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-357">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="976aa-358">データ コンテキストによって、データ モデルに含めるエンティティが指定されます:</span><span class="sxs-lookup"><span data-stu-id="976aa-358">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="976aa-359">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-359">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="976aa-360">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="976aa-360">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="976aa-361">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="976aa-361">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="976aa-362">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-362">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="976aa-363">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="976aa-363">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="976aa-364">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="976aa-364">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="976aa-365">DB コンテキストを作成し、それを DI コンテナーに登録しました。</span><span class="sxs-lookup"><span data-stu-id="976aa-365">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="976aa-366">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="976aa-366">Test the app</span></span>

* <span data-ttu-id="976aa-367">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="976aa-367">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="976aa-368">次のようなデータベースの例外が表示された場合:</span><span class="sxs-lookup"><span data-stu-id="976aa-368">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="976aa-369">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="976aa-369">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="976aa-370">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="976aa-370">Test the **Create** link.</span></span> <span data-ttu-id="976aa-371">データを入力して送信します。</span><span class="sxs-lookup"><span data-stu-id="976aa-371">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="976aa-372">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-372">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="976aa-373">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-373">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="976aa-374">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="976aa-374">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="976aa-375">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="976aa-375">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="976aa-376">次の `Startup` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="976aa-376">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="976aa-377">上のコードで強調表示されている部分は、[依存性の注入](xref:fundamentals/dependency-injection)コンテナーに追加されているムービー データベース コンテキストを示します。</span><span class="sxs-lookup"><span data-stu-id="976aa-377">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="976aa-378">`services.AddDbContext<MvcMovieContext>(options =>` では、使用するデータベースと接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="976aa-378">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="976aa-379">`=>` は[ラムダ演算子](/dotnet/articles/csharp/language-reference/operators/lambda-operator)です。</span><span class="sxs-lookup"><span data-stu-id="976aa-379">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="976aa-380">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="976aa-380">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="976aa-381">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="976aa-381">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="976aa-382">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-382">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="976aa-383">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="976aa-383">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="976aa-384">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="976aa-384">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="976aa-385">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="976aa-385">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="976aa-386">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="976aa-386">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="976aa-387">この厳密に型指定された方法を使うと、コンパイル時のコードのチェックが向上します。</span><span class="sxs-lookup"><span data-stu-id="976aa-387">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="976aa-388">スキャフォールディング メカニズムは、メソッドとビューを作成するときに、`MoviesController` クラスとビューでこの方法 (つまり、厳密に型指定されたモデルを渡すこと) を使いました。</span><span class="sxs-lookup"><span data-stu-id="976aa-388">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="976aa-389">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="976aa-389">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="976aa-390">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-390">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="976aa-391">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="976aa-391">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="976aa-392">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="976aa-392">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="976aa-393">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="976aa-393">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="976aa-394">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="976aa-394">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="976aa-395">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="976aa-395">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="976aa-396">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-396">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="976aa-397">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-397">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="976aa-398">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-398">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="976aa-399">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-399">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="976aa-400">ビュー ファイルの先頭に `@model` ステートメントを含めることで、ビューが期待するオブジェクトの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="976aa-400">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="976aa-401">ムービー コントローラーを作成したとき、*Details.cshtml* ファイルの先頭に次の `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="976aa-401">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="976aa-402">この `@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="976aa-402">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="976aa-403">たとえば、*Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="976aa-403">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="976aa-404">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="976aa-404">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="976aa-405">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-405">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="976aa-406">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="976aa-406">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="976aa-407">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="976aa-407">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="976aa-408">ムービー コントローラーを作成したとき、スキャフォールディングによって *Index.cshtml* ファイルの先頭に `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="976aa-408">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="976aa-409">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="976aa-409">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="976aa-410">たとえば、*Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="976aa-410">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="976aa-411">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="976aa-411">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="976aa-412">それ以外の利点としては、コンパイル時にコードのチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="976aa-412">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="976aa-413">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="976aa-413">Additional resources</span></span>

* [<span data-ttu-id="976aa-414">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="976aa-414">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="976aa-415">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="976aa-415">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="976aa-416">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル データベースの使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="976aa-416">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
