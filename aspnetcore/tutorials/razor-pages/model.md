---
title: ASP.NET Core での Razor ページ アプリへのモデルの追加
author: rick-anderson
description: Entity Framework Core (EF Core) を利用し、データベースでムービーを管理するためのクラスを追加する方法について説明します。
ms.author: riande
ms.date: 11/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f2c9c2fc8112ef8a1a5afdbe448de6319c43521d
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761225"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="b4828-103">ASP.NET Core での Razor ページ アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="b4828-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="b4828-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b4828-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b4828-105">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="b4828-106">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="b4828-107">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="b4828-108">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="b4828-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="b4828-109">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b4828-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b4828-110">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="b4828-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="b4828-111">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="b4828-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4828-113">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="b4828-114">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-114">Name the folder *Models*.</span></span>

<span data-ttu-id="b4828-115">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b4828-115">Right click the *Models* folder.</span></span> <span data-ttu-id="b4828-116">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="b4828-117">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b4828-119">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="b4828-120">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b4828-122">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="b4828-123">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="b4828-124">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="b4828-125">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b4828-126">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="b4828-127">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="b4828-128">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="b4828-129">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b4828-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b4828-130">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b4828-130">Scaffold the movie model</span></span>

<span data-ttu-id="b4828-131">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="b4828-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b4828-132">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4828-134">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4828-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b4828-135">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b4828-136">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-136">Name the folder *Movies*</span></span>

<span data-ttu-id="b4828-137">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="b4828-139">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="b4828-141">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="b4828-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b4828-142">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="b4828-143">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie.**Models**.RazorPagesMovieContext を RazorPagesMovie.**Data**.RazorPagesMovieContext に変更します。</span><span class="sxs-lookup"><span data-stu-id="b4828-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="b4828-144">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="b4828-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="b4828-145">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="b4828-146">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="b4828-146">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="b4828-148">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b4828-150">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4828-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b4828-151">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4828-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b4828-152">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="b4828-153">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b4828-155">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4828-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b4828-156">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4828-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b4828-157">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="b4828-158">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="b4828-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4828-160">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="b4828-161">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="b4828-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="b4828-162">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="b4828-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="b4828-163">更新済み</span><span class="sxs-lookup"><span data-stu-id="b4828-163">Updated</span></span>

* <span data-ttu-id="b4828-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b4828-164">*Startup.cs*</span></span>

<span data-ttu-id="b4828-165">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="b4828-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b4828-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b4828-167">スキャフォールディングのプロセスでは、次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="b4828-168">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="b4828-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="b4828-169">作成されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="b4828-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b4828-170">最初の移行</span><span class="sxs-lookup"><span data-stu-id="b4828-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4828-172">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="b4828-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="b4828-173">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-173">Add an initial migration.</span></span>
* <span data-ttu-id="b4828-174">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="b4828-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="b4828-175">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b4828-177">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="b4828-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="b4828-180">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="b4828-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="b4828-181">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="b4828-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="b4828-182">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="b4828-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="b4828-183">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="b4828-184">移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="b4828-185">このスキーマは、`DbContext` で指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="b4828-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="b4828-186">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="b4828-187">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="b4828-188">`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="b4828-189">この場合、`update` により、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b4828-191">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="b4828-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b4828-192">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="b4828-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b4828-193">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b4828-194">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b4828-195">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="b4828-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b4828-196">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="b4828-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b4828-197">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b4828-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b4828-198">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="b4828-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="b4828-199">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b4828-200">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b4828-201">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="b4828-202">上記のコードによって、エンティティ セットの [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-202">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b4828-203">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="b4828-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b4828-204">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="b4828-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b4828-205">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b4828-206">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b4828-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b4828-208">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b4828-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b4828-210">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b4828-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b4828-211">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b4828-211">Test the app</span></span>

* <span data-ttu-id="b4828-212">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="b4828-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b4828-213">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b4828-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b4828-214">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b4828-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b4828-215">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4828-215">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="b4828-217">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b4828-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b4828-218">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4828-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b4828-219">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b4828-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b4828-220">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4828-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="b4828-221">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="b4828-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4828-222">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b4828-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b4828-223">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="b4828-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b4828-224">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="b4828-225">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="b4828-226">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="b4828-227">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="b4828-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="b4828-228">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b4828-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b4828-229">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="b4828-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="b4828-230">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="b4828-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4828-232">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="b4828-233">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-233">Name the folder *Models*.</span></span>

<span data-ttu-id="b4828-234">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b4828-234">Right click the *Models* folder.</span></span> <span data-ttu-id="b4828-235">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="b4828-236">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b4828-238">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="b4828-239">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-240">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b4828-241">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="b4828-242">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="b4828-243">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="b4828-244">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b4828-245">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="b4828-246">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="b4828-247">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="b4828-248">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b4828-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b4828-249">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b4828-249">Scaffold the movie model</span></span>

<span data-ttu-id="b4828-250">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="b4828-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b4828-251">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4828-253">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4828-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b4828-254">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b4828-255">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4828-255">Name the folder *Movies*</span></span>

<span data-ttu-id="b4828-256">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="b4828-258">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="b4828-260">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="b4828-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="b4828-261">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="b4828-262">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="b4828-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="b4828-263">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="b4828-263">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arp.png)

<span data-ttu-id="b4828-265">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b4828-267">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4828-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b4828-268">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4828-268">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b4828-269">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-269">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="b4828-270">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-270">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-271">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-271">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b4828-272">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4828-272">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b4828-273">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4828-273">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b4828-274">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-274">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="b4828-275">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-275">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="b4828-276">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="b4828-276">Files created</span></span>

* <span data-ttu-id="b4828-277">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="b4828-277">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="b4828-278">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="b4828-278">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="b4828-279">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="b4828-279">File updated</span></span>

* <span data-ttu-id="b4828-280">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b4828-280">*Startup.cs*</span></span>

<span data-ttu-id="b4828-281">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="b4828-281">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b4828-282">最初の移行</span><span class="sxs-lookup"><span data-stu-id="b4828-282">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4828-284">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="b4828-284">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="b4828-285">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="b4828-285">Add an initial migration.</span></span>
* <span data-ttu-id="b4828-286">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="b4828-286">Update the database with the initial migration.</span></span>

<span data-ttu-id="b4828-287">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4828-287">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b4828-289">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="b4828-289">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="b4828-290">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-290">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="b4828-291">このスキーマは、`DbContext` で指定されたモデルに基づきます (*RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="b4828-291">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="b4828-292">`InitialCreate` 引数は、移行の名前を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-292">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="b4828-293">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-293">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="b4828-294">詳細については、<xref:data/ef-mvc/migrations> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b4828-294">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="b4828-295">`Update-Database` コマンドは、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4828-295">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="b4828-296">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-296">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-298">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="b4828-299">上記のコマンドで次の警告が生成されます。"*エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。* " この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-299">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4828-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4828-300">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b4828-301">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="b4828-301">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b4828-302">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="b4828-302">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b4828-303">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-303">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b4828-304">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-304">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b4828-305">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="b4828-305">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b4828-306">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="b4828-306">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b4828-307">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b4828-307">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b4828-308">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="b4828-308">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="b4828-309">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-309">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b4828-310">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-310">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b4828-311">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-311">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="b4828-312">上記のコードによって、エンティティ セットの [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-312">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b4828-313">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="b4828-313">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b4828-314">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="b4828-314">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b4828-315">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b4828-315">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b4828-316">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b4828-316">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4828-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4828-317">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b4828-318">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b4828-318">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4828-319">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4828-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b4828-320">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b4828-320">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b4828-321">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b4828-321">Test the app</span></span>

* <span data-ttu-id="b4828-322">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="b4828-322">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b4828-323">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b4828-323">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b4828-324">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b4828-324">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b4828-325">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4828-325">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="b4828-327">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b4828-327">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b4828-328">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4828-328">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b4828-329">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b4828-329">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b4828-330">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4828-330">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="b4828-331">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="b4828-331">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4828-332">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b4828-332">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b4828-333">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="b4828-333">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
