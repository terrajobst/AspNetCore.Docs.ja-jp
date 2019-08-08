---
title: ASP.NET Core での Razor ページ アプリへのモデルの追加
author: rick-anderson
description: Entity Framework Core (EF Core) を利用し、データベースでムービーを管理するためのクラスを追加する方法について説明します。
ms.author: riande
ms.date: 07/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6132f7b907014b4f57bb9ae0300e00b6ecb23f1a
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820070"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="78d17-103">ASP.NET Core での Razor ページ アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="78d17-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="78d17-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="78d17-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78d17-105">このセクションでは、データベースで映画を管理するクラスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="78d17-106">これらのクラスは、データベースを操作するために [Entity Framework Core](/ef/core) (EF Core) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="78d17-107">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="78d17-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="78d17-108">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="78d17-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="78d17-109">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="78d17-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="78d17-110">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="78d17-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78d17-112">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="78d17-113">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-113">Name the folder *Models*.</span></span>

<span data-ttu-id="78d17-114">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="78d17-114">Right click the *Models* folder.</span></span> <span data-ttu-id="78d17-115">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="78d17-116">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="78d17-118">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="78d17-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="78d17-119">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="78d17-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78d17-121">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="78d17-122">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="78d17-123">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="78d17-124">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="78d17-125">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="78d17-126">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="78d17-127">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="78d17-128">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="78d17-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="78d17-129">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="78d17-129">Scaffold the movie model</span></span>

<span data-ttu-id="78d17-130">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="78d17-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="78d17-131">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78d17-133">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="78d17-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="78d17-134">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="78d17-135">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-135">Name the folder *Movies*</span></span>

<span data-ttu-id="78d17-136">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="78d17-138">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="78d17-140">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="78d17-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="78d17-141">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="78d17-142">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie.**Models**.RazorPagesMovieContext を RazorPagesMovie.**Data**.RazorPagesMovieContext に変更します。</span><span class="sxs-lookup"><span data-stu-id="78d17-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="78d17-143">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="78d17-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="78d17-144">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="78d17-145">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="78d17-145">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="78d17-147">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="78d17-149">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="78d17-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="78d17-150">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="78d17-150">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="78d17-151">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-151">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="78d17-152">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-152">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78d17-154">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="78d17-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="78d17-155">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="78d17-155">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="78d17-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="78d17-157">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-157">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="78d17-158">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="78d17-158">Files created</span></span>

* <span data-ttu-id="78d17-159">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="78d17-159">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="78d17-160">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="78d17-160">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="78d17-161">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="78d17-161">File updated</span></span>

* <span data-ttu-id="78d17-162">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="78d17-162">*Startup.cs*</span></span>

<span data-ttu-id="78d17-163">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="78d17-163">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="78d17-164">最初の移行</span><span class="sxs-lookup"><span data-stu-id="78d17-164">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78d17-166">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="78d17-166">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="78d17-167">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="78d17-167">Add an initial migration.</span></span>
* <span data-ttu-id="78d17-168">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="78d17-168">Update the database with the initial migration.</span></span>

<span data-ttu-id="78d17-169">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-169">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="78d17-171">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="78d17-171">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="78d17-174">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="78d17-174">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="78d17-175">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="78d17-175">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="78d17-176">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="78d17-176">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="78d17-177">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-177">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="78d17-178">`ef migrations add InitialCreate` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-178">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="78d17-179">このスキーマは、`DbContext` で指定されたモデルに基づきます (*RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="78d17-179">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="78d17-180">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-180">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="78d17-181">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-181">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="78d17-182">`ef database update` コマンドは、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-182">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="78d17-183">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-183">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-184">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="78d17-185">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="78d17-185">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="78d17-186">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="78d17-186">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="78d17-187">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-187">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="78d17-188">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-188">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="78d17-189">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="78d17-189">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="78d17-190">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="78d17-190">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="78d17-191">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="78d17-191">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="78d17-192">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="78d17-192">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="78d17-193">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-193">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="78d17-194">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-194">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="78d17-195">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-195">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="78d17-196">上記のコードによって、エンティティ セットの [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-196">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="78d17-197">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="78d17-197">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="78d17-198">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="78d17-198">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="78d17-199">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-199">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="78d17-200">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="78d17-200">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="78d17-202">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="78d17-202">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-203">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="78d17-204">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="78d17-204">Examine the `Up` method.</span></span>

---

<span data-ttu-id="78d17-205">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-205">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="78d17-206">このスキーマは、`RazorPagesMovieContext` で指定されたモデルに基づきます (*Data/RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="78d17-206">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="78d17-207">`Initial` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-207">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="78d17-208">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-208">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="78d17-209">詳細については、<xref:data/ef-mvc/migrations> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78d17-209">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="78d17-210">`Update-Database` コマンドは、データベースを作成する、*Migrations/{time-stamp}_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-210">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="78d17-211">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="78d17-211">Test the app</span></span>

* <span data-ttu-id="78d17-212">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="78d17-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="78d17-213">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="78d17-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="78d17-214">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="78d17-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="78d17-215">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="78d17-215">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="78d17-217">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="78d17-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="78d17-218">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="78d17-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="78d17-219">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="78d17-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="78d17-220">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="78d17-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="78d17-221">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="78d17-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78d17-222">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="78d17-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="78d17-223">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="78d17-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="78d17-224">このセクションでは、データベースで映画を管理するクラスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-224">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="78d17-225">これらのクラスは、データベースを操作するために [Entity Framework Core](/ef/core) (EF Core) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-225">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="78d17-226">EF Core は、データ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="78d17-226">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="78d17-227">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="78d17-227">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="78d17-228">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="78d17-228">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="78d17-229">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="78d17-229">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78d17-231">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-231">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="78d17-232">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-232">Name the folder *Models*.</span></span>

<span data-ttu-id="78d17-233">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="78d17-233">Right click the *Models* folder.</span></span> <span data-ttu-id="78d17-234">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-234">Select **Add** > **Class**.</span></span> <span data-ttu-id="78d17-235">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-235">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="78d17-237">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="78d17-237">Add a folder named *Models*.</span></span>
* <span data-ttu-id="78d17-238">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="78d17-238">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-239">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78d17-240">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-240">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="78d17-241">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-241">Name the folder *Models*.</span></span>
* <span data-ttu-id="78d17-242">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-242">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="78d17-243">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-243">In the **New File** dialog:</span></span>

  * <span data-ttu-id="78d17-244">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-244">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="78d17-245">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-245">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="78d17-246">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-246">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="78d17-247">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="78d17-247">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="78d17-248">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="78d17-248">Scaffold the movie model</span></span>

<span data-ttu-id="78d17-249">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="78d17-249">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="78d17-250">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-250">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78d17-252">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="78d17-252">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="78d17-253">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-253">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="78d17-254">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="78d17-254">Name the folder *Movies*</span></span>

<span data-ttu-id="78d17-255">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-255">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="78d17-257">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-257">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="78d17-259">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="78d17-259">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="78d17-260">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-260">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="78d17-261">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="78d17-261">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="78d17-262">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="78d17-262">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arp.png)

<span data-ttu-id="78d17-264">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-264">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-265">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-265">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="78d17-266">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="78d17-266">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="78d17-267">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="78d17-267">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="78d17-268">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-268">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="78d17-269">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-269">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-270">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-270">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78d17-271">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="78d17-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="78d17-272">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="78d17-272">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="78d17-273">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-273">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="78d17-274">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-274">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="78d17-275">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="78d17-275">Files created</span></span>

* <span data-ttu-id="78d17-276">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="78d17-276">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="78d17-277">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="78d17-277">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="78d17-278">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="78d17-278">File updated</span></span>

* <span data-ttu-id="78d17-279">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="78d17-279">*Startup.cs*</span></span>

<span data-ttu-id="78d17-280">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="78d17-280">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="78d17-281">最初の移行</span><span class="sxs-lookup"><span data-stu-id="78d17-281">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-282">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-282">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78d17-283">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="78d17-283">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="78d17-284">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="78d17-284">Add an initial migration.</span></span>
* <span data-ttu-id="78d17-285">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="78d17-285">Update the database with the initial migration.</span></span>

<span data-ttu-id="78d17-286">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="78d17-286">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="78d17-288">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="78d17-288">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-289">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-289">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-290">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="78d17-291">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="78d17-291">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="78d17-292">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="78d17-292">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="78d17-293">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="78d17-293">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="78d17-294">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-294">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="78d17-295">`ef migrations add InitialCreate` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-295">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="78d17-296">このスキーマは、`DbContext` で指定されたモデルに基づきます (*RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="78d17-296">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="78d17-297">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-297">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="78d17-298">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-298">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="78d17-299">`ef database update` コマンドは、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-299">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="78d17-300">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-300">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78d17-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78d17-301">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="78d17-302">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="78d17-302">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="78d17-303">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="78d17-303">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="78d17-304">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-304">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="78d17-305">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-305">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="78d17-306">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="78d17-306">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="78d17-307">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="78d17-307">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="78d17-308">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="78d17-308">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="78d17-309">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="78d17-309">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="78d17-310">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-310">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="78d17-311">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-311">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="78d17-312">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-312">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="78d17-313">上記のコードによって、エンティティ セットの [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-313">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="78d17-314">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="78d17-314">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="78d17-315">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="78d17-315">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="78d17-316">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-316">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="78d17-317">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="78d17-317">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78d17-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78d17-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="78d17-319">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="78d17-319">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78d17-320">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78d17-320">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="78d17-321">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="78d17-321">Examine the `Up` method.</span></span>

---

<span data-ttu-id="78d17-322">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-322">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="78d17-323">このスキーマは、`RazorPagesMovieContext` で指定されたモデルに基づきます (*Data/RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="78d17-323">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="78d17-324">`Initial` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-324">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="78d17-325">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="78d17-325">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="78d17-326">詳細については、<xref:data/ef-mvc/migrations> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78d17-326">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="78d17-327">`Update-Database` コマンドは、データベースを作成する、*Migrations/{time-stamp}_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="78d17-327">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="78d17-328">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="78d17-328">Test the app</span></span>

* <span data-ttu-id="78d17-329">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="78d17-329">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="78d17-330">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="78d17-330">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="78d17-331">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="78d17-331">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="78d17-332">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="78d17-332">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="78d17-334">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="78d17-334">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="78d17-335">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="78d17-335">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="78d17-336">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="78d17-336">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="78d17-337">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="78d17-337">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="78d17-338">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="78d17-338">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78d17-339">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="78d17-339">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="78d17-340">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="78d17-340">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
