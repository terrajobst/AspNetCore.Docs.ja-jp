---
title: ASP.NET Core での Razor ページ アプリへのモデルの追加
author: rick-anderson
description: Entity Framework Core (EF Core) を利用し、データベースでムービーを管理するためのクラスを追加する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 9d9266ae08c7abe747d4497bbcf52778cf2e370e
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2020
ms.locfileid: "76268765"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="12f53-103">ASP.NET Core での Razor ページ アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="12f53-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="12f53-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="12f53-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="12f53-105">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="12f53-106">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="12f53-107">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="12f53-108">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="12f53-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="12f53-109">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="12f53-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="12f53-110">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="12f53-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="12f53-111">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="12f53-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f53-113">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="12f53-114">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-114">Name the folder *Models*.</span></span>

<span data-ttu-id="12f53-115">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="12f53-115">Right click the *Models* folder.</span></span> <span data-ttu-id="12f53-116">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="12f53-117">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="12f53-119">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="12f53-120">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="12f53-122">Solution Pad で、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="12f53-123">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="12f53-124">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="12f53-125">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="12f53-126">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="12f53-127">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="12f53-128">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="12f53-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="12f53-129">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="12f53-129">Scaffold the movie model</span></span>

<span data-ttu-id="12f53-130">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="12f53-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="12f53-131">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f53-133">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="12f53-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="12f53-134">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="12f53-135">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-135">Name the folder *Movies*</span></span>

<span data-ttu-id="12f53-136">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="12f53-138">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="12f53-140">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="12f53-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="12f53-141">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="12f53-142">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie.**Models**.RazorPagesMovieContext を RazorPagesMovie.**Data**.RazorPagesMovieContext に変更します。</span><span class="sxs-lookup"><span data-stu-id="12f53-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="12f53-143">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="12f53-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="12f53-144">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="12f53-145">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="12f53-145">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="12f53-147">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="12f53-149">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="12f53-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="12f53-150">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="12f53-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="12f53-151">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="12f53-152">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12f53-154">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="12f53-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="12f53-155">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="12f53-156">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-156">Name the folder *Movies*</span></span>

<span data-ttu-id="12f53-157">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[新しいスキャフォールディング]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="12f53-159">**[新しいスキャフォールディング]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="12f53-161">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="12f53-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="12f53-162">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="12f53-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="12f53-163">**データ コンテキスト クラス**行で、新しいクラスの名前「RazorPagesMovie.**Data**.RazorPagesMovieContext」を入力します。</span><span class="sxs-lookup"><span data-stu-id="12f53-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="12f53-164">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="12f53-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="12f53-165">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="12f53-166">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="12f53-166">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="12f53-168">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="12f53-169">EF ツールの追加</span><span class="sxs-lookup"><span data-stu-id="12f53-169">Add EF tools</span></span>

<span data-ttu-id="12f53-170">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="12f53-171">上記のコマンドを実行すると、.NET Core CLI の Entity Framework Core ツールが追加されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="12f53-172">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="12f53-172">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f53-174">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="12f53-175">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="12f53-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="12f53-176">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="12f53-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="12f53-177">更新済み</span><span class="sxs-lookup"><span data-stu-id="12f53-177">Updated</span></span>

* <span data-ttu-id="12f53-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="12f53-178">*Startup.cs*</span></span>

<span data-ttu-id="12f53-179">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="12f53-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12f53-181">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="12f53-182">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="12f53-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="12f53-183">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="12f53-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="12f53-184">更新済み</span><span class="sxs-lookup"><span data-stu-id="12f53-184">Updated</span></span>

* <span data-ttu-id="12f53-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="12f53-185">*Startup.cs*</span></span>

<span data-ttu-id="12f53-186">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="12f53-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="12f53-188">スキャフォールディングのプロセスでは、次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="12f53-189">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="12f53-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="12f53-190">作成されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="12f53-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="12f53-191">最初の移行</span><span class="sxs-lookup"><span data-stu-id="12f53-191">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f53-193">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="12f53-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="12f53-194">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-194">Add an initial migration.</span></span>
* <span data-ttu-id="12f53-195">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="12f53-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="12f53-196">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="12f53-198">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="12f53-198">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="12f53-201">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="12f53-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="12f53-202">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="12f53-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="12f53-203">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="12f53-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="12f53-204">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="12f53-205">移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="12f53-206">このスキーマは、`DbContext` で指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="12f53-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="12f53-207">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="12f53-208">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="12f53-209">`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="12f53-210">この場合、`update` により、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="12f53-212">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="12f53-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="12f53-213">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="12f53-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="12f53-214">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="12f53-215">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="12f53-216">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="12f53-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="12f53-217">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="12f53-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="12f53-218">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="12f53-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="12f53-219">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="12f53-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="12f53-220">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="12f53-221">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="12f53-222">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="12f53-223">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="12f53-224">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="12f53-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="12f53-225">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="12f53-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="12f53-226">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="12f53-227">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="12f53-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="12f53-229">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="12f53-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-230">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12f53-231">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="12f53-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="12f53-232">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="12f53-232">Test the app</span></span>

* <span data-ttu-id="12f53-233">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="12f53-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="12f53-234">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="12f53-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="12f53-235">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="12f53-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="12f53-236">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="12f53-236">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="12f53-238">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="12f53-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="12f53-239">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12f53-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="12f53-240">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="12f53-240">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="12f53-241">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="12f53-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="12f53-242">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="12f53-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12f53-243">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="12f53-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="12f53-244">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="12f53-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="12f53-245">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="12f53-246">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="12f53-247">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="12f53-248">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="12f53-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="12f53-249">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="12f53-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="12f53-250">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="12f53-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="12f53-251">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="12f53-251">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f53-253">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="12f53-254">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-254">Name the folder *Models*.</span></span>

<span data-ttu-id="12f53-255">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="12f53-255">Right click the *Models* folder.</span></span> <span data-ttu-id="12f53-256">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="12f53-257">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="12f53-259">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="12f53-260">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-261">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="12f53-262">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="12f53-263">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="12f53-264">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="12f53-265">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="12f53-266">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="12f53-267">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="12f53-268">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="12f53-269">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="12f53-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="12f53-270">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="12f53-270">Scaffold the movie model</span></span>

<span data-ttu-id="12f53-271">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="12f53-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="12f53-272">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-273">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f53-274">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="12f53-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="12f53-275">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="12f53-276">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-276">Name the folder *Movies*</span></span>

<span data-ttu-id="12f53-277">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="12f53-279">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="12f53-281">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="12f53-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="12f53-282">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="12f53-283">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="12f53-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="12f53-284">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="12f53-284">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arp.png)

<span data-ttu-id="12f53-286">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="12f53-288">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="12f53-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="12f53-289">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="12f53-290">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-291">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12f53-292">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="12f53-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="12f53-293">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="12f53-294">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="12f53-294">Name the folder *Movies*</span></span>

<span data-ttu-id="12f53-295">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="12f53-297">**[新しいスキャフォールディングの追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="12f53-299">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="12f53-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="12f53-300">**[モデル クラス]** ドロップ ダウンで、 **[Movie]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="12f53-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="12f53-301">**データ コンテキスト クラス** 行で、 **[RazorPagesMovieContext]** を選択または入力します。これにより、新しい db コンテキスト クラスが正しい名前空間で作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="12f53-302">このクラスでは、**RazorPagesMovie.Models.RazorPagesMovieContext** となります。</span><span class="sxs-lookup"><span data-stu-id="12f53-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="12f53-303">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="12f53-303">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="12f53-305">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="12f53-306">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="12f53-307">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="12f53-307">Files created</span></span>

* <span data-ttu-id="12f53-308">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="12f53-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="12f53-309">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="12f53-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="12f53-310">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="12f53-310">File updated</span></span>

* <span data-ttu-id="12f53-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="12f53-311">*Startup.cs*</span></span>

<span data-ttu-id="12f53-312">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="12f53-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="12f53-313">最初の移行</span><span class="sxs-lookup"><span data-stu-id="12f53-313">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f53-315">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="12f53-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="12f53-316">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="12f53-316">Add an initial migration.</span></span>
* <span data-ttu-id="12f53-317">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="12f53-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="12f53-318">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="12f53-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="12f53-320">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="12f53-320">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="12f53-321">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="12f53-322">このスキーマは、`DbContext` で指定されたモデルに基づきます (*RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="12f53-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="12f53-323">`InitialCreate` 引数は、移行の名前を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="12f53-324">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="12f53-325">詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12f53-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="12f53-326">`Update-Database` コマンドは、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="12f53-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="12f53-327">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-329">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="12f53-330">上記のコマンドで次の警告が生成されます。"*エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。* " この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="12f53-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f53-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="12f53-332">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="12f53-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="12f53-333">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="12f53-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="12f53-334">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="12f53-335">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="12f53-336">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="12f53-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="12f53-337">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="12f53-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="12f53-338">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="12f53-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="12f53-339">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="12f53-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="12f53-340">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="12f53-341">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="12f53-342">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="12f53-343">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="12f53-344">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="12f53-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="12f53-345">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="12f53-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="12f53-346">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="12f53-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="12f53-347">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="12f53-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="12f53-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f53-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="12f53-349">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="12f53-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="12f53-350">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f53-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12f53-351">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="12f53-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="12f53-352">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="12f53-352">Test the app</span></span>

* <span data-ttu-id="12f53-353">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="12f53-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="12f53-354">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="12f53-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="12f53-355">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="12f53-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="12f53-356">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="12f53-356">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="12f53-358">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="12f53-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="12f53-359">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12f53-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="12f53-360">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="12f53-360">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="12f53-361">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="12f53-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="12f53-362">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="12f53-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12f53-363">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="12f53-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="12f53-364">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="12f53-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
