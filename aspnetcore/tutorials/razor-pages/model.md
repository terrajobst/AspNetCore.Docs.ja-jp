---
title: ASP.NET Core での Razor ページ アプリへのモデルの追加
author: rick-anderson
description: Entity Framework Core (EF Core) を利用し、データベースでムービーを管理するためのクラスを追加する方法について説明します。
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4f8b80cb51bd10eb3b136a780dc123c41d61c0a5
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519079"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="841d3-103">ASP.NET Core での Razor ページ アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="841d3-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="841d3-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="841d3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="841d3-105">このセクションでは、データベースで映画を管理するクラスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="841d3-106">これらのクラスは、データベースを操作するために [Entity Framework Core](/ef/core) (EF Core) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="841d3-107">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="841d3-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="841d3-108">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="841d3-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="841d3-109">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="841d3-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="841d3-110">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="841d3-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="841d3-112">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="841d3-113">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-113">Name the folder *Models*.</span></span>

<span data-ttu-id="841d3-114">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="841d3-114">Right click the *Models* folder.</span></span> <span data-ttu-id="841d3-115">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="841d3-116">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="841d3-118">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="841d3-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="841d3-119">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="841d3-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="841d3-121">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="841d3-122">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="841d3-123">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="841d3-124">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="841d3-125">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="841d3-126">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="841d3-127">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="841d3-128">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="841d3-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="841d3-129">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="841d3-129">Scaffold the movie model</span></span>

<span data-ttu-id="841d3-130">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="841d3-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="841d3-131">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="841d3-133">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="841d3-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="841d3-134">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="841d3-135">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-135">Name the folder *Movies*</span></span>

<span data-ttu-id="841d3-136">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="841d3-138">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="841d3-140">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="841d3-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="841d3-141">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="841d3-142">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie.**Models**.RazorPagesMovieContext を RazorPagesMovie.**Data**.RazorPagesMovieContext に変更します。</span><span class="sxs-lookup"><span data-stu-id="841d3-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="841d3-143">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="841d3-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="841d3-144">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="841d3-145">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="841d3-145">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="841d3-147">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="841d3-149">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="841d3-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="841d3-150">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="841d3-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="841d3-151">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="841d3-152">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="841d3-154">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="841d3-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="841d3-155">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="841d3-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="841d3-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="841d3-157">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="841d3-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="841d3-159">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="841d3-160">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="841d3-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="841d3-161">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="841d3-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="841d3-162">更新済み</span><span class="sxs-lookup"><span data-stu-id="841d3-162">Updated</span></span>

* <span data-ttu-id="841d3-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="841d3-163">*Startup.cs*</span></span>

<span data-ttu-id="841d3-164">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="841d3-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="841d3-165">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="841d3-166">スキャフォールディングのプロセスでは、次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="841d3-167">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="841d3-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="841d3-168">作成されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="841d3-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="841d3-169">最初の移行</span><span class="sxs-lookup"><span data-stu-id="841d3-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="841d3-171">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="841d3-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="841d3-172">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="841d3-172">Add an initial migration.</span></span>
* <span data-ttu-id="841d3-173">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="841d3-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="841d3-174">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="841d3-176">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="841d3-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-178">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="841d3-179">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="841d3-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="841d3-180">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="841d3-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="841d3-181">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="841d3-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="841d3-182">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="841d3-183">移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-183">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="841d3-184">このスキーマは、`DbContext` で指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="841d3-184">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="841d3-185">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="841d3-186">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="841d3-187">`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-187">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="841d3-188">この場合、`update` により、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-188">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="841d3-190">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="841d3-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="841d3-191">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="841d3-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="841d3-192">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="841d3-193">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="841d3-194">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="841d3-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="841d3-195">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="841d3-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="841d3-196">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="841d3-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="841d3-197">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="841d3-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="841d3-198">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="841d3-199">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="841d3-200">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="841d3-201">上記のコードによって、エンティティ セットの [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="841d3-202">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="841d3-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="841d3-203">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="841d3-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="841d3-204">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="841d3-205">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="841d3-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="841d3-207">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="841d3-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-208">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="841d3-209">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="841d3-209">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="841d3-210">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="841d3-210">Test the app</span></span>

* <span data-ttu-id="841d3-211">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="841d3-211">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="841d3-212">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="841d3-212">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="841d3-213">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="841d3-213">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="841d3-214">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="841d3-214">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="841d3-216">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="841d3-216">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="841d3-217">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="841d3-217">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="841d3-218">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="841d3-218">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="841d3-219">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="841d3-219">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="841d3-220">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="841d3-220">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="841d3-221">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="841d3-221">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="841d3-222">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="841d3-222">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="841d3-223">このセクションでは、データベースで映画を管理するクラスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-223">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="841d3-224">これらのクラスは、データベースを操作するために [Entity Framework Core](/ef/core) (EF Core) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-224">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="841d3-225">EF Core は、データ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="841d3-225">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="841d3-226">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="841d3-226">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="841d3-227">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="841d3-227">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="841d3-228">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="841d3-228">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="841d3-230">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-230">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="841d3-231">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-231">Name the folder *Models*.</span></span>

<span data-ttu-id="841d3-232">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="841d3-232">Right click the *Models* folder.</span></span> <span data-ttu-id="841d3-233">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-233">Select **Add** > **Class**.</span></span> <span data-ttu-id="841d3-234">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-234">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="841d3-236">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="841d3-236">Add a folder named *Models*.</span></span>
* <span data-ttu-id="841d3-237">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="841d3-237">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-238">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="841d3-239">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-239">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="841d3-240">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-240">Name the folder *Models*.</span></span>
* <span data-ttu-id="841d3-241">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-241">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="841d3-242">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-242">In the **New File** dialog:</span></span>

  * <span data-ttu-id="841d3-243">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-243">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="841d3-244">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-244">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="841d3-245">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-245">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="841d3-246">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="841d3-246">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="841d3-247">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="841d3-247">Scaffold the movie model</span></span>

<span data-ttu-id="841d3-248">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="841d3-248">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="841d3-249">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-249">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="841d3-251">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="841d3-251">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="841d3-252">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-252">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="841d3-253">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="841d3-253">Name the folder *Movies*</span></span>

<span data-ttu-id="841d3-254">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-254">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="841d3-256">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-256">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="841d3-258">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="841d3-258">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="841d3-259">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-259">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="841d3-260">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="841d3-260">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="841d3-261">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="841d3-261">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arp.png)

<span data-ttu-id="841d3-263">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-263">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-264">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="841d3-265">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="841d3-265">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="841d3-266">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="841d3-266">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="841d3-267">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-267">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="841d3-268">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-268">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-269">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-269">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="841d3-270">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="841d3-270">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="841d3-271">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="841d3-271">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="841d3-272">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-272">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="841d3-273">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-273">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="841d3-274">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="841d3-274">Files created</span></span>

* <span data-ttu-id="841d3-275">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="841d3-275">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="841d3-276">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="841d3-276">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="841d3-277">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="841d3-277">File updated</span></span>

* <span data-ttu-id="841d3-278">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="841d3-278">*Startup.cs*</span></span>

<span data-ttu-id="841d3-279">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="841d3-279">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="841d3-280">最初の移行</span><span class="sxs-lookup"><span data-stu-id="841d3-280">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="841d3-282">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="841d3-282">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="841d3-283">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="841d3-283">Add an initial migration.</span></span>
* <span data-ttu-id="841d3-284">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="841d3-284">Update the database with the initial migration.</span></span>

<span data-ttu-id="841d3-285">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="841d3-285">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="841d3-287">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="841d3-287">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="841d3-288">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-288">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="841d3-289">このスキーマは、`DbContext` で指定されたモデルに基づきます (*RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="841d3-289">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="841d3-290">`InitialCreate` 引数は、移行の名前を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-290">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="841d3-291">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-291">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="841d3-292">詳細については、<xref:data/ef-mvc/migrations> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="841d3-292">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="841d3-293">`Update-Database` コマンドは、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="841d3-293">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="841d3-294">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-294">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-296">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-296">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="841d3-297">上記のコマンドで次の警告が生成されます。"*エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。* " この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-297">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="841d3-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="841d3-298">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="841d3-299">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="841d3-299">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="841d3-300">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="841d3-300">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="841d3-301">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-301">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="841d3-302">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-302">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="841d3-303">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="841d3-303">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="841d3-304">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="841d3-304">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="841d3-305">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="841d3-305">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="841d3-306">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="841d3-306">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="841d3-307">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-307">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="841d3-308">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-308">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="841d3-309">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-309">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="841d3-310">上記のコードによって、エンティティ セットの [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-310">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="841d3-311">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="841d3-311">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="841d3-312">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="841d3-312">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="841d3-313">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="841d3-313">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="841d3-314">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="841d3-314">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="841d3-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="841d3-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="841d3-316">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="841d3-316">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="841d3-317">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="841d3-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="841d3-318">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="841d3-318">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="841d3-319">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="841d3-319">Test the app</span></span>

* <span data-ttu-id="841d3-320">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="841d3-320">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="841d3-321">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="841d3-321">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="841d3-322">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="841d3-322">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="841d3-323">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="841d3-323">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="841d3-325">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="841d3-325">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="841d3-326">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="841d3-326">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="841d3-327">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="841d3-327">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="841d3-328">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="841d3-328">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="841d3-329">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="841d3-329">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="841d3-330">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="841d3-330">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="841d3-331">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="841d3-331">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
