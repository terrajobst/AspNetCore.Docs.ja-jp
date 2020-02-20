---
title: ASP.NET Core の Razor ページと EF Core - 移行 - 4/8
author: rick-anderson
description: このチュートリアルでは、ASP.NET Core MVC アプリでデータ モデルの変更を管理するための EF Core の移行機能の使用を開始します。
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: f8e78cf7230528a4047eac1d52a12e2ed8392dec
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213444"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="e6136-103">ASP.NET Core の Razor ページと EF Core - 移行 - 4/8</span><span class="sxs-lookup"><span data-stu-id="e6136-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="e6136-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6136-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6136-105">このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能を紹介します。</span><span class="sxs-lookup"><span data-stu-id="e6136-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="e6136-106">新しいアプリを開発するときには、頻繁にデータ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="e6136-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="e6136-107">モデルを変更するたびに、モデルはデータベースと同期されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e6136-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="e6136-108">このチュートリアル シリーズでは、最初にデータベースが存在しない場合に、データベースを作成するように Entity Framework を構成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="e6136-109">データ モデルが変更されるたびに、データベースを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="e6136-110">次にアプリが実行されると、`EnsureCreated` への呼び出しによって、新しいデータ モデルに一致するデータベースが再作成されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="e6136-111">その後、`DbInitializer` クラスが実行され、新しいデータベースがシードされます。</span><span class="sxs-lookup"><span data-stu-id="e6136-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="e6136-112">このアプローチは、実稼働環境にアプリを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="e6136-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="e6136-113">アプリが実稼働環境で実行されているときには、通常は維持する必要があるデータをアプリが格納します。</span><span class="sxs-lookup"><span data-stu-id="e6136-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="e6136-114">変更 (新しい列の追加など) が加えられるたびに、テスト データベースを使用してアプリを開始することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6136-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="e6136-115">EF Core の移行機能では、新しいデータベースを作成する代わりに、EF Core でデータベース スキーマを更新できるようにすることで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="e6136-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="e6136-116">データ モデルが変更されたときにデータベースを削除して再作成する代わりに、移行によってスキーマを更新し、既存のデータを維持します。</span><span class="sxs-lookup"><span data-stu-id="e6136-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="e6136-117">データベースを削除する</span><span class="sxs-lookup"><span data-stu-id="e6136-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6136-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6136-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6136-119">**SQL Server オブジェクトエクスプローラー** (SSOX) を使用してデータベースを削除するか、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6136-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6136-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e6136-121">EF CLI をインストールするには、コマンド プロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="e6136-122">コマンド プロンプトで、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="e6136-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="e6136-123">プロジェクトフォルダーには、*ContosoUniversity.csproj* ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e6136-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="e6136-124">*CU.db* ファイルを削除するか、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="e6136-125">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="e6136-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6136-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6136-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6136-127">PMC で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6136-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6136-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e6136-129">コマンド プロンプトがプロジェクト フォルダーにあることを確認し、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="e6136-130">Up メソッドと Down メソッド</span><span class="sxs-lookup"><span data-stu-id="e6136-130">Up and Down methods</span></span>

<span data-ttu-id="e6136-131">EF Core の `migrations add` コマンドでは、データベースを作成するコードが生成されました。</span><span class="sxs-lookup"><span data-stu-id="e6136-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="e6136-132">この移行コードは *Migrations\<timestamp>_InitialCreate.cs* ファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="e6136-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e6136-133">`InitialCreate` クラスの `Up` メソッドでは、データ モデルのエンティティ セットに対応するデータベース テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="e6136-134">次の例で示すように、`Down` メソッドは、それらを削除します。</span><span class="sxs-lookup"><span data-stu-id="e6136-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="e6136-135">上記のコードは、初期の移行のためのコードです。</span><span class="sxs-lookup"><span data-stu-id="e6136-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="e6136-136">コード:</span><span class="sxs-lookup"><span data-stu-id="e6136-136">The code:</span></span>

* <span data-ttu-id="e6136-137">`migrations add InitialCreate` コマンドによって生成されました。</span><span class="sxs-lookup"><span data-stu-id="e6136-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="e6136-138">`database update` コマンドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="e6136-139">データベース コンテキスト クラスによって指定されたデータ モデル用のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="e6136-140">移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="e6136-141">移行名には、任意の有効なファイル名を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e6136-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="e6136-142">移行で行われている処理をまとめた単語または語句を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e6136-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="e6136-143">たとえば、department テーブルを追加する移行に "AddDepartmentTable" という名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e6136-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="e6136-144">移行履歴テーブル</span><span class="sxs-lookup"><span data-stu-id="e6136-144">The migrations history table</span></span>

* <span data-ttu-id="e6136-145">SSOX または SQLite ツールを使用してデータベースを検査します。</span><span class="sxs-lookup"><span data-stu-id="e6136-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="e6136-146">`__EFMigrationsHistory` テーブルが追加されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e6136-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="e6136-147">`__EFMigrationsHistory` テーブルは、どの移行がデータベースに適用されたかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="e6136-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="e6136-148">`__EFMigrationsHistory` テーブル内のデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="e6136-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="e6136-149">最初の移行に対する 1 行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="e6136-150">データ モデルのスナップショット</span><span class="sxs-lookup"><span data-stu-id="e6136-150">The data model snapshot</span></span>

<span data-ttu-id="e6136-151">移行によって、現在のデータ モデルの*スナップショット*が *Migrations/SchoolContextModelSnapshot.cs* 内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="e6136-152">移行を追加するときに、EF では、スナップショット ファイルと現在のデータ モデルを比較することによって変更内容を判断します。</span><span class="sxs-lookup"><span data-stu-id="e6136-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="e6136-153">スナップショット ファイルではデータ モデルの状態が追跡されるため、`<timestamp>_<migrationname>.cs` ファイルを削除することによって移行を削除することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6136-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="e6136-154">最新の移行を取り消すには、`migrations remove` コマンドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="e6136-155">このコマンドでは移行が削除され、スナップショットが正しく確実にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="e6136-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="e6136-156">詳細については、「[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6136-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="e6136-157">Remove EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="e6136-157">Remove EnsureCreated</span></span>

<span data-ttu-id="e6136-158">このチュートリアル シリーズは、`EnsureCreated` を使用して開始されました。</span><span class="sxs-lookup"><span data-stu-id="e6136-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="e6136-159">`EnsureCreated` では、移行履歴テーブルが作成されないため、移行に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6136-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="e6136-160">これは、データベースが頻繁に削除および再作成されるようなテストや迅速なプロトタイプのために設計されています。</span><span class="sxs-lookup"><span data-stu-id="e6136-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="e6136-161">これ以降、チュートリアルでは移行が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="e6136-162">*Data/DBInitializer.cs* で、次の行をコメント アウトします。</span><span class="sxs-lookup"><span data-stu-id="e6136-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="e6136-163">アプリを実行し、データベースがシードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e6136-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="e6136-164">運用環境で移行を適用する</span><span class="sxs-lookup"><span data-stu-id="e6136-164">Applying migrations in production</span></span>

<span data-ttu-id="e6136-165">実稼働アプリでは、アプリケーションの起動時に [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) を呼び出さ**ない**ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e6136-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="e6136-166">`Migrate` は、サーバー ファームに展開されているアプリから呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="e6136-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="e6136-167">アプリが複数のサーバー インスタンスにスケールアウトされている場合は、確実にデータベース スキーマの更新が複数のサーバーから行われたり、読み取り/書き込みアクセスと競合したりしないようにするのは困難です。</span><span class="sxs-lookup"><span data-stu-id="e6136-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="e6136-168">データベースの移行は、展開の一部として制御された方法で行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="e6136-169">実稼働データベースの移行には次の方法があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="e6136-170">移行を使用して SQL スクリプトを作成し、展開で SQL スクリプトを使用します。</span><span class="sxs-lookup"><span data-stu-id="e6136-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="e6136-171">制御された環境から `dotnet ef database update` を実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e6136-172">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="e6136-172">Troubleshooting</span></span>

<span data-ttu-id="e6136-173">アプリで SQL Server LocalDB を使用し、次の例外が表示される場合:</span><span class="sxs-lookup"><span data-stu-id="e6136-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="e6136-174">このソリューションでは、コマンド プロンプトで `dotnet ef database update` を実行する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="e6136-175">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e6136-175">Additional resources</span></span>

* <span data-ttu-id="e6136-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet)</span><span class="sxs-lookup"><span data-stu-id="e6136-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="e6136-177">パッケージ マネージャー コンソール (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="e6136-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="e6136-178">次の手順</span><span class="sxs-lookup"><span data-stu-id="e6136-178">Next steps</span></span>

<span data-ttu-id="e6136-179">次のチュートリアルでは、エンティティのプロパティと新しいエンティティを追加して、データ モデルを構築します。</span><span class="sxs-lookup"><span data-stu-id="e6136-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e6136-180">[前のチュートリアル](xref:data/ef-rp/sort-filter-page)
> [次のチュートリアル](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="e6136-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6136-181">このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="e6136-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="e6136-182">解決できない問題が発生した場合は、[完成したアプリ](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)をダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="e6136-182">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="e6136-183">新しいアプリを開発するときには、頻繁にデータ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="e6136-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="e6136-184">モデルを変更するたびに、モデルはデータベースと同期されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e6136-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="e6136-185">このチュートリアルでは、最初にデータベースが存在しない場合にデータベースを作成するように Entity Framework を構成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="e6136-186">データ モデルが変更されるたびに次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-186">Each time the data model changes:</span></span>

* <span data-ttu-id="e6136-187">データベースが削除されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-187">The DB is dropped.</span></span>
* <span data-ttu-id="e6136-188">EF がモデルと一致する新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="e6136-189">アプリがテスト データを DB に格納します。</span><span class="sxs-lookup"><span data-stu-id="e6136-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="e6136-190">このアプローチは、実稼働環境にアプリを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="e6136-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="e6136-191">アプリが実稼働環境で実行されているときには、通常は維持する必要があるデータをアプリが格納します。</span><span class="sxs-lookup"><span data-stu-id="e6136-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="e6136-192">変更 (新しい列の追加など) が加えられるたびにテスト データベースを使用してアプリを開始することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6136-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="e6136-193">EF コア移行機能は、新しいデータベースを作成する代わりに EF Core でデータベース スキーマを更新できるようにすることでこの問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="e6136-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="e6136-194">データ モデルが変更されたときにデータベースを削除して再作成する代わりに、移行によってスキーマを更新し、既存のデータを維持します。</span><span class="sxs-lookup"><span data-stu-id="e6136-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="e6136-195">データベースを削除する</span><span class="sxs-lookup"><span data-stu-id="e6136-195">Drop the database</span></span>

<span data-ttu-id="e6136-196">**SQL Server オブジェクト エクスプローラー** (SSOX) または `database drop` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e6136-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6136-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6136-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6136-198">**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="e6136-199">PMC から `Get-Help about_EntityFrameworkCore` を実行してヘルプ情報を入手します。</span><span class="sxs-lookup"><span data-stu-id="e6136-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6136-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6136-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e6136-201">コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="e6136-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="e6136-202">プロジェクト フォルダーには *Startup.cs* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e6136-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="e6136-203">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="e6136-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="e6136-204">初期移行を作成し、DB を更新する</span><span class="sxs-lookup"><span data-stu-id="e6136-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="e6136-205">プロジェクトをビルドし、最初の移行を作成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6136-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6136-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6136-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6136-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="e6136-208">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="e6136-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="e6136-209">EF Core コマンド `migrations add` で、DB の作成元のコードが生成されました。</span><span class="sxs-lookup"><span data-stu-id="e6136-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="e6136-210">この移行コードは *Migrations\<timestamp>_InitialCreate.cs* ファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="e6136-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e6136-211">`InitialCreate` クラスの `Up` メソッドは、データ モデルのエンティティ セットに対応するデータベース テーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="e6136-212">次の例で示すように、`Down` メソッドは、それらを削除します。</span><span class="sxs-lookup"><span data-stu-id="e6136-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="e6136-213">移行は、`Up` メソッドを呼び出して、移行のためのデータ モデルの変更を実装します。</span><span class="sxs-lookup"><span data-stu-id="e6136-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="e6136-214">更新をロールバックするためのコマンドを入力すると、移行が `Down` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e6136-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="e6136-215">上記のコードは、初期の移行のためのコードです。</span><span class="sxs-lookup"><span data-stu-id="e6136-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="e6136-216">そのコードは、`migrations add InitialCreate` コマンドが実行されたときに作成されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="e6136-217">移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="e6136-218">移行名には、任意の有効なファイル名を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e6136-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="e6136-219">移行で行われている処理をまとめた単語または語句を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e6136-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="e6136-220">たとえば、department テーブルを追加する移行に "AddDepartmentTable" という名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e6136-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="e6136-221">最初の移行が作成され、データベースが存在している場合:</span><span class="sxs-lookup"><span data-stu-id="e6136-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="e6136-222">データベース作成コードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="e6136-223">データベースは既にデータ モデルと一致しているため、データベース作成コードを実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e6136-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="e6136-224">データベース作成コードが実行された場合、データベースは既にデータ モデルと一致しているため、何も変更しません。</span><span class="sxs-lookup"><span data-stu-id="e6136-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="e6136-225">新しい環境にアプリが展開されるときには、データベースを作成するためのデータベース作成コードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="e6136-226">以前は DB が削除されて存在しないため、移行によって新しい DB が作成されていました。</span><span class="sxs-lookup"><span data-stu-id="e6136-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="e6136-227">データ モデルのスナップショット</span><span class="sxs-lookup"><span data-stu-id="e6136-227">The data model snapshot</span></span>

<span data-ttu-id="e6136-228">移行は、現在のデータベース スキーマの*スナップショット*を *Migrations/SchoolContextModelSnapshot.cs* 内に作成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="e6136-229">移行を追加するときに、EF は、スナップショット ファイルとデータ モデルを比較することによって変更内容を判断します。</span><span class="sxs-lookup"><span data-stu-id="e6136-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="e6136-230">移行を削除するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e6136-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6136-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6136-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6136-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="e6136-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6136-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6136-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="e6136-234">詳細については、「[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6136-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="e6136-235">remove migrations コマンドによって移行が削除され、スナップショットが正しくリセットされたことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="e6136-236">EnsureCreated を削除し、アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="e6136-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="e6136-237">初期の開発では、`EnsureCreated` が使用されました。</span><span class="sxs-lookup"><span data-stu-id="e6136-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="e6136-238">このチュートリアルでは、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="e6136-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="e6136-239">`EnsureCreated` には次の制限が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="e6136-240">移行をバイパスし、データベースとスキーマを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="e6136-241">移行テーブルは作成しません。</span><span class="sxs-lookup"><span data-stu-id="e6136-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="e6136-242">移行と共に使用することは*できません*。</span><span class="sxs-lookup"><span data-stu-id="e6136-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="e6136-243">データベースが頻繁に削除および再作成されるようなテストや迅速なプロトタイプのために設計されています。</span><span class="sxs-lookup"><span data-stu-id="e6136-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="e6136-244">`EnsureCreated` を削除します。</span><span class="sxs-lookup"><span data-stu-id="e6136-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="e6136-245">アプリを実行し、DB がシードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e6136-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="e6136-246">データベースを検査する</span><span class="sxs-lookup"><span data-stu-id="e6136-246">Inspect the database</span></span>

<span data-ttu-id="e6136-247">**SQL Server オブジェクト エクスプローラー**を使用してデータベースを検査します。</span><span class="sxs-lookup"><span data-stu-id="e6136-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="e6136-248">`__EFMigrationsHistory` テーブルが追加されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e6136-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="e6136-249">`__EFMigrationsHistory` テーブルは、どの移行がデータベースに適用されたかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="e6136-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="e6136-250">`__EFMigrationsHistory` テーブルのデータを表示すると、最初の移行の 1 つの行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e6136-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="e6136-251">前の CLI の出力例の最後のログは、この行を作成する INSERT ステートメントを示しています。</span><span class="sxs-lookup"><span data-stu-id="e6136-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="e6136-252">アプリを実行して、すべてが適切に機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e6136-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="e6136-253">運用環境で移行を適用する</span><span class="sxs-lookup"><span data-stu-id="e6136-253">Applying migrations in production</span></span>

<span data-ttu-id="e6136-254">実稼働アプリケーションでは、アプリケーションの起動時に [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) を**呼び出さない**ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e6136-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="e6136-255">`Migrate` をサーバー ファームのアプリから呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="e6136-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="e6136-256">たとえば、アプリがスケールアウト (アプリの複数のインスタンスを実行する) を使用してクラウドに展開されている場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="e6136-257">データベースの移行は、展開の一部として制御された方法で行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="e6136-258">実稼働データベースの移行には次の方法があります。</span><span class="sxs-lookup"><span data-stu-id="e6136-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="e6136-259">移行を使用して SQL スクリプトを作成し、展開で SQL スクリプトを使用します。</span><span class="sxs-lookup"><span data-stu-id="e6136-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="e6136-260">制御された環境から `dotnet ef database update` を実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="e6136-261">EF Core は、`__MigrationsHistory` テーブルを使用して、移行を実行する必要があるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e6136-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="e6136-262">データベースが最新の状態になっている場合、移行は実行されません。</span><span class="sxs-lookup"><span data-stu-id="e6136-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e6136-263">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="e6136-263">Troubleshooting</span></span>

<span data-ttu-id="e6136-264">[完成したアプリ](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)をダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="e6136-264">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="e6136-265">アプリは次の例外を生成します。</span><span class="sxs-lookup"><span data-stu-id="e6136-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="e6136-266">解決方法 : `dotnet ef database update` を実行します。</span><span class="sxs-lookup"><span data-stu-id="e6136-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="e6136-267">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e6136-267">Additional resources</span></span>

* [<span data-ttu-id="e6136-268">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="e6136-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="e6136-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="e6136-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="e6136-270">パッケージ マネージャー コンソール (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="e6136-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="e6136-271">[前へ](xref:data/ef-rp/sort-filter-page)
> [次へ](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="e6136-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

