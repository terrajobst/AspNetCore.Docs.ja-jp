---
title: ASP.NET Core での Razor ページ アプリへのモデルの追加
author: rick-anderson
description: Entity Framework Core (EF Core) を利用し、データベースでムービーを管理するためのクラスを追加する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0934c94236b507f2f57200ded4344a71c483d356
ms.sourcegitcommit: 5fe17e54f7e4267a2fdecc6f9aa1d41166cecc34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737859"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="99c76-103">ASP.NET Core での Razor ページ アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="99c76-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="99c76-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="99c76-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="99c76-105">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="99c76-106">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="99c76-107">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="99c76-108">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="99c76-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="99c76-109">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="99c76-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="99c76-110">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="99c76-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="99c76-111">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="99c76-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99c76-113">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="99c76-114">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-114">Name the folder *Models*.</span></span>

<span data-ttu-id="99c76-115">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99c76-115">Right click the *Models* folder.</span></span> <span data-ttu-id="99c76-116">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="99c76-117">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="99c76-119">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="99c76-120">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="99c76-122">Solution Pad で、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="99c76-123">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="99c76-124">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="99c76-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="99c76-125">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="99c76-126">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="99c76-127">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="99c76-128">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="99c76-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="99c76-129">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="99c76-129">Scaffold the movie model</span></span>

<span data-ttu-id="99c76-130">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="99c76-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="99c76-131">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99c76-133">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="99c76-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="99c76-134">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="99c76-135">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-135">Name the folder *Movies*</span></span>

<span data-ttu-id="99c76-136">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="99c76-138">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="99c76-140">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="99c76-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="99c76-141">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="99c76-142">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie.**Models**.RazorPagesMovieContext を RazorPagesMovie.**Data**.RazorPagesMovieContext に変更します。</span><span class="sxs-lookup"><span data-stu-id="99c76-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="99c76-143">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="99c76-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="99c76-144">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="99c76-145">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="99c76-145">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="99c76-147">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="99c76-149">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="99c76-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="99c76-150">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="99c76-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="99c76-151">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99c76-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="99c76-152">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99c76-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99c76-154">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="99c76-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="99c76-155">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="99c76-156">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-156">Name the folder *Movies*</span></span>

<span data-ttu-id="99c76-157">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[新しいスキャフォールディング]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="99c76-159">**[新しいスキャフォールディング]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="99c76-161">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="99c76-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="99c76-162">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="99c76-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="99c76-163">**データ コンテキスト クラス**行で、新しいクラスの名前「RazorPagesMovie.**Data**.RazorPagesMovieContext」を入力します。</span><span class="sxs-lookup"><span data-stu-id="99c76-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="99c76-164">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="99c76-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="99c76-165">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="99c76-166">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="99c76-166">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="99c76-168">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="99c76-169">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="99c76-169">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99c76-171">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-171">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="99c76-172">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="99c76-172">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="99c76-173">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="99c76-173">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="99c76-174">更新済み</span><span class="sxs-lookup"><span data-stu-id="99c76-174">Updated</span></span>

* <span data-ttu-id="99c76-175">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="99c76-175">*Startup.cs*</span></span>

<span data-ttu-id="99c76-176">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="99c76-176">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99c76-178">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-178">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="99c76-179">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="99c76-179">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="99c76-180">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="99c76-180">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="99c76-181">更新済み</span><span class="sxs-lookup"><span data-stu-id="99c76-181">Updated</span></span>

* <span data-ttu-id="99c76-182">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="99c76-182">*Startup.cs*</span></span>

<span data-ttu-id="99c76-183">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="99c76-183">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-184">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-184">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="99c76-185">スキャフォールディングのプロセスでは、次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-185">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="99c76-186">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="99c76-186">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="99c76-187">作成されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="99c76-187">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="99c76-188">最初の移行</span><span class="sxs-lookup"><span data-stu-id="99c76-188">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99c76-190">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="99c76-190">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="99c76-191">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-191">Add an initial migration.</span></span>
* <span data-ttu-id="99c76-192">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="99c76-192">Update the database with the initial migration.</span></span>

<span data-ttu-id="99c76-193">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-193">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="99c76-195">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="99c76-195">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-197">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="99c76-198">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="99c76-198">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="99c76-199">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="99c76-199">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="99c76-200">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="99c76-200">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="99c76-201">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-201">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="99c76-202">移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-202">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="99c76-203">このスキーマは、`DbContext` で指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="99c76-203">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="99c76-204">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-204">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="99c76-205">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-205">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="99c76-206">`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-206">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="99c76-207">この場合、`update` により、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-207">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-208">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="99c76-209">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="99c76-209">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="99c76-210">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="99c76-210">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="99c76-211">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-211">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="99c76-212">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-212">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="99c76-213">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="99c76-213">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="99c76-214">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="99c76-214">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="99c76-215">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="99c76-215">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="99c76-216">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="99c76-216">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="99c76-217">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-217">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="99c76-218">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-218">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="99c76-219">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-219">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="99c76-220">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-220">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="99c76-221">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="99c76-221">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="99c76-222">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="99c76-222">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="99c76-223">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-223">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="99c76-224">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="99c76-224">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-225">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-225">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="99c76-226">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="99c76-226">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99c76-228">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="99c76-228">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="99c76-229">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="99c76-229">Test the app</span></span>

* <span data-ttu-id="99c76-230">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="99c76-230">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="99c76-231">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="99c76-231">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="99c76-232">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="99c76-232">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="99c76-233">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="99c76-233">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="99c76-235">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="99c76-235">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="99c76-236">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99c76-236">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="99c76-237">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="99c76-237">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="99c76-238">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="99c76-238">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="99c76-239">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="99c76-239">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99c76-240">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="99c76-240">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="99c76-241">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="99c76-241">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="99c76-242">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-242">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="99c76-243">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-243">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="99c76-244">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-244">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="99c76-245">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="99c76-245">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="99c76-246">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="99c76-246">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="99c76-247">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="99c76-247">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="99c76-248">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="99c76-248">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-249">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-249">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99c76-250">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-250">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="99c76-251">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-251">Name the folder *Models*.</span></span>

<span data-ttu-id="99c76-252">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99c76-252">Right click the *Models* folder.</span></span> <span data-ttu-id="99c76-253">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-253">Select **Add** > **Class**.</span></span> <span data-ttu-id="99c76-254">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-254">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-255">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-255">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="99c76-256">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-256">Add a folder named *Models*.</span></span>
* <span data-ttu-id="99c76-257">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-257">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-258">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-258">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="99c76-259">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-259">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="99c76-260">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-260">Name the folder *Models*.</span></span>
* <span data-ttu-id="99c76-261">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-261">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="99c76-262">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="99c76-262">In the **New File** dialog:</span></span>

  * <span data-ttu-id="99c76-263">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-263">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="99c76-264">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-264">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="99c76-265">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-265">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="99c76-266">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="99c76-266">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="99c76-267">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="99c76-267">Scaffold the movie model</span></span>

<span data-ttu-id="99c76-268">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="99c76-268">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="99c76-269">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-269">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-270">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99c76-271">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="99c76-271">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="99c76-272">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-272">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="99c76-273">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-273">Name the folder *Movies*</span></span>

<span data-ttu-id="99c76-274">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-274">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="99c76-276">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-276">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="99c76-278">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="99c76-278">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="99c76-279">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-279">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="99c76-280">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="99c76-280">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="99c76-281">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="99c76-281">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arp.png)

<span data-ttu-id="99c76-283">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-283">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-284">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-284">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="99c76-285">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="99c76-285">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="99c76-286">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99c76-286">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="99c76-287">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99c76-287">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-288">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99c76-289">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="99c76-289">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="99c76-290">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-290">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="99c76-291">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99c76-291">Name the folder *Movies*</span></span>

<span data-ttu-id="99c76-292">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-292">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="99c76-294">**[新しいスキャフォールディングの追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-294">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="99c76-296">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="99c76-296">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="99c76-297">**[モデル クラス]** ドロップ ダウンで、 **[Movie]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="99c76-297">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="99c76-298">**データ コンテキスト クラス** 行で、 **[RazorPagesMovieContext]** を選択または入力します。これにより、新しい db コンテキスト クラスが正しい名前空間で作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-298">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="99c76-299">このクラスでは、**RazorPagesMovie.Models.RazorPagesMovieContext** となります。</span><span class="sxs-lookup"><span data-stu-id="99c76-299">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="99c76-300">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="99c76-300">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="99c76-302">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-302">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="99c76-303">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-303">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="99c76-304">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="99c76-304">Files created</span></span>

* <span data-ttu-id="99c76-305">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="99c76-305">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="99c76-306">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="99c76-306">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="99c76-307">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="99c76-307">File updated</span></span>

* <span data-ttu-id="99c76-308">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="99c76-308">*Startup.cs*</span></span>

<span data-ttu-id="99c76-309">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="99c76-309">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="99c76-310">最初の移行</span><span class="sxs-lookup"><span data-stu-id="99c76-310">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99c76-312">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="99c76-312">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="99c76-313">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="99c76-313">Add an initial migration.</span></span>
* <span data-ttu-id="99c76-314">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="99c76-314">Update the database with the initial migration.</span></span>

<span data-ttu-id="99c76-315">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99c76-315">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="99c76-317">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="99c76-317">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="99c76-318">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-318">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="99c76-319">このスキーマは、`DbContext` で指定されたモデルに基づきます (*RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="99c76-319">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="99c76-320">`InitialCreate` 引数は、移行の名前を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-320">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="99c76-321">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-321">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="99c76-322">詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99c76-322">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="99c76-323">`Update-Database` コマンドは、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99c76-323">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="99c76-324">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-324">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-326">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="99c76-327">上記のコマンドで次の警告が生成されます。"*エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。* " この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-327">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99c76-328">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99c76-328">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="99c76-329">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="99c76-329">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="99c76-330">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="99c76-330">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="99c76-331">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-331">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="99c76-332">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-332">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="99c76-333">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="99c76-333">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="99c76-334">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="99c76-334">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="99c76-335">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="99c76-335">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="99c76-336">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="99c76-336">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="99c76-337">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-337">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="99c76-338">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-338">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="99c76-339">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-339">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="99c76-340">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-340">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="99c76-341">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="99c76-341">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="99c76-342">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="99c76-342">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="99c76-343">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="99c76-343">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="99c76-344">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="99c76-344">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="99c76-345">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99c76-345">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="99c76-346">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="99c76-346">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="99c76-347">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99c76-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99c76-348">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="99c76-348">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="99c76-349">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="99c76-349">Test the app</span></span>

* <span data-ttu-id="99c76-350">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="99c76-350">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="99c76-351">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="99c76-351">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="99c76-352">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="99c76-352">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="99c76-353">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="99c76-353">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="99c76-355">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="99c76-355">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="99c76-356">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99c76-356">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="99c76-357">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="99c76-357">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="99c76-358">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="99c76-358">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="99c76-359">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="99c76-359">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99c76-360">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="99c76-360">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="99c76-361">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="99c76-361">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
