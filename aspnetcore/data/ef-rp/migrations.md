---
title: ASP.NET Core の Razor ページと EF Core - 移行 - 4/8
author: rick-anderson
description: このチュートリアルでは、ASP.NET Core MVC アプリでデータ モデルの変更を管理するための EF Core の移行機能の使用を開始します。
ms.author: riande
ms.date: 06/30/2017
uid: data/ef-rp/migrations
ms.openlocfilehash: 54225a8126e04eb4ff3a6a0cde9d305249299887
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223860"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="f38f6-103">ASP.NET Core の Razor ページと EF Core - 移行 - 4/8</span><span class="sxs-lookup"><span data-stu-id="f38f6-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="f38f6-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f38f6-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="f38f6-105">このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-105">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="f38f6-106">解決できない問題が発生した場合は、[完成したアプリ](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)をダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="f38f6-106">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="f38f6-107">新しいアプリを開発するときには、頻繁にデータ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-107">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="f38f6-108">モデルを変更するたびに、モデルはデータベースと同期されなくなります。</span><span class="sxs-lookup"><span data-stu-id="f38f6-108">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="f38f6-109">このチュートリアルでは、最初にデータベースが存在しない場合にデータベースを作成するように Entity Framework を構成します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-109">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="f38f6-110">データ モデルが変更されるたびに次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-110">Each time the data model changes:</span></span>

* <span data-ttu-id="f38f6-111">データベースが削除されます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-111">The DB is dropped.</span></span>
* <span data-ttu-id="f38f6-112">EF がモデルと一致する新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-112">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="f38f6-113">アプリがテスト データを DB に格納します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-113">The app seeds the DB with test data.</span></span>

<span data-ttu-id="f38f6-114">このアプローチは、実稼働環境にアプリを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-114">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="f38f6-115">アプリが実稼働環境で実行されているときには、通常は維持する必要があるデータをアプリが格納します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-115">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="f38f6-116">変更 (新しい列の追加など) が加えられるたびにテスト データベースを使用してアプリを開始することはできません。</span><span class="sxs-lookup"><span data-stu-id="f38f6-116">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="f38f6-117">EF コア移行機能は、新しいデータベースを作成する代わりに EF Core でデータベース スキーマを更新できるようにすることでこの問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-117">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="f38f6-118">データ モデルが変更されたときにデータベースを削除して再作成する代わりに、移行によってスキーマを更新し、既存のデータを維持します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-118">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="f38f6-119">データベースを削除する</span><span class="sxs-lookup"><span data-stu-id="f38f6-119">Drop the database</span></span>

<span data-ttu-id="f38f6-120">**SQL Server オブジェクト エクスプローラー** (SSOX) または `database drop` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-120">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f38f6-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f38f6-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f38f6-122">**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-122">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="f38f6-123">PMC から `Get-Help about_EntityFrameworkCore` を実行してヘルプ情報を入手します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-123">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f38f6-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f38f6-124">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f38f6-125">コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-125">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="f38f6-126">プロジェクト フォルダーには *Startup.cs* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-126">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="f38f6-127">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-127">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="f38f6-128">初期移行を作成し、DB を更新する</span><span class="sxs-lookup"><span data-stu-id="f38f6-128">Create an initial migration and update the DB</span></span>

<span data-ttu-id="f38f6-129">プロジェクトをビルドし、最初の移行を作成します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-129">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f38f6-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f38f6-130">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f38f6-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f38f6-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="f38f6-132">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="f38f6-132">Examine the Up and Down methods</span></span>

<span data-ttu-id="f38f6-133">EF Core コマンド `migrations add` で、DB の作成元のコードが生成されました。</span><span class="sxs-lookup"><span data-stu-id="f38f6-133">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="f38f6-134">この移行コードは *Migrations\<timestamp>_InitialCreate.cs* ファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="f38f6-134">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="f38f6-135">`InitialCreate` クラスの `Up` メソッドは、データ モデルのエンティティ セットに対応するデータベース テーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-135">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="f38f6-136">次の例で示すように、`Down` メソッドは、それらを削除します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-136">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="f38f6-137">移行は、`Up` メソッドを呼び出して、移行のためのデータ モデルの変更を実装します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-137">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="f38f6-138">更新をロールバックするためのコマンドを入力すると、移行が `Down` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-138">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="f38f6-139">上記のコードは、初期の移行のためのコードです。</span><span class="sxs-lookup"><span data-stu-id="f38f6-139">The preceding code is for the initial migration.</span></span> <span data-ttu-id="f38f6-140">そのコードは、`migrations add InitialCreate` コマンドが実行されたときに作成されます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-140">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="f38f6-141">移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-141">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="f38f6-142">移行名には、任意の有効なファイル名を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-142">The migration name can be any valid file name.</span></span> <span data-ttu-id="f38f6-143">移行で行われている処理をまとめた単語または語句を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f38f6-143">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="f38f6-144">たとえば、department テーブルを追加する移行に "AddDepartmentTable" という名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-144">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="f38f6-145">最初の移行が作成され、データベースが存在している場合:</span><span class="sxs-lookup"><span data-stu-id="f38f6-145">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="f38f6-146">データベース作成コードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-146">The DB creation code is generated.</span></span>
* <span data-ttu-id="f38f6-147">データベースは既にデータ モデルと一致しているため、データベース作成コードを実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f38f6-147">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="f38f6-148">データベース作成コードが実行された場合、データベースは既にデータ モデルと一致しているため、何も変更しません。</span><span class="sxs-lookup"><span data-stu-id="f38f6-148">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="f38f6-149">新しい環境にアプリが展開されるときには、データベースを作成するためのデータベース作成コードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f38f6-149">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="f38f6-150">以前は DB が削除されて存在しないため、移行によって新しい DB が作成されていました。</span><span class="sxs-lookup"><span data-stu-id="f38f6-150">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="f38f6-151">データ モデルのスナップショット</span><span class="sxs-lookup"><span data-stu-id="f38f6-151">The data model snapshot</span></span>

<span data-ttu-id="f38f6-152">移行は、現在のデータベース スキーマの*スナップショット*を *Migrations/SchoolContextModelSnapshot.cs* 内に作成します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-152">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="f38f6-153">移行を追加するときに、EF は、スナップショット ファイルとデータ モデルを比較することによって変更内容を判断します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-153">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="f38f6-154">移行を削除するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-154">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f38f6-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f38f6-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f38f6-156">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="f38f6-156">Remove-Migration</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f38f6-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f38f6-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations remove
```

<span data-ttu-id="f38f6-158">詳細については、「[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f38f6-158">For more information, see  [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="f38f6-159">remove migrations コマンドによって移行が削除され、スナップショットが正しくリセットされたことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-159">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="f38f6-160">EnsureCreated を削除し、アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="f38f6-160">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="f38f6-161">初期の開発では、`EnsureCreated` が使用されました。</span><span class="sxs-lookup"><span data-stu-id="f38f6-161">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="f38f6-162">このチュートリアルでは、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-162">In this tutorial, migrations are used.</span></span> <span data-ttu-id="f38f6-163">`EnsureCreated` には次の制限が適用されます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-163">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="f38f6-164">移行をバイパスし、データベースとスキーマを作成します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-164">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="f38f6-165">移行テーブルは作成しません。</span><span class="sxs-lookup"><span data-stu-id="f38f6-165">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="f38f6-166">移行と共に使用することは*できません*。</span><span class="sxs-lookup"><span data-stu-id="f38f6-166">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="f38f6-167">データベースが頻繁に削除および再作成されるようなテストや迅速なプロトタイプのために設計されています。</span><span class="sxs-lookup"><span data-stu-id="f38f6-167">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="f38f6-168">`EnsureCreated` を削除します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-168">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="f38f6-169">アプリを実行し、DB がシードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-169">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="f38f6-170">データベースを検査する</span><span class="sxs-lookup"><span data-stu-id="f38f6-170">Inspect the database</span></span>

<span data-ttu-id="f38f6-171">**SQL Server オブジェクト エクスプローラー**を使用してデータベースを検査します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-171">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="f38f6-172">`__EFMigrationsHistory` テーブルが追加されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f38f6-172">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="f38f6-173">`__EFMigrationsHistory` テーブルは、どの移行がデータベースに適用されたかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-173">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="f38f6-174">`__EFMigrationsHistory` テーブルのデータを表示すると、最初の移行の 1 つの行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f38f6-174">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="f38f6-175">前の CLI の出力例の最後のログは、この行を作成する INSERT ステートメントを示しています。</span><span class="sxs-lookup"><span data-stu-id="f38f6-175">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="f38f6-176">アプリを実行して、すべてが適切に機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-176">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="f38f6-177">運用環境で移行を適用する</span><span class="sxs-lookup"><span data-stu-id="f38f6-177">Applying migrations in production</span></span>

<span data-ttu-id="f38f6-178">実稼働アプリケーションでは、アプリケーションの起動時に [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) を**呼び出さない**ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f38f6-178">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="f38f6-179">`Migrate` をサーバー ファームのアプリから呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="f38f6-179">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="f38f6-180">たとえば、アプリがスケールアウト (アプリの複数のインスタンスを実行する) を使用してクラウドに展開されている場合があります。</span><span class="sxs-lookup"><span data-stu-id="f38f6-180">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="f38f6-181">データベースの移行は、展開の一部として制御された方法で行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="f38f6-181">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="f38f6-182">実稼働データベースの移行には次の方法があります。</span><span class="sxs-lookup"><span data-stu-id="f38f6-182">Production database migration approaches include:</span></span>

* <span data-ttu-id="f38f6-183">移行を使用して SQL スクリプトを作成し、展開で SQL スクリプトを使用します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-183">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="f38f6-184">制御された環境から `dotnet ef database update` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-184">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="f38f6-185">EF Core は、`__MigrationsHistory` テーブルを使用して、移行を実行する必要があるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-185">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="f38f6-186">データベースが最新の状態になっている場合、移行は実行されません。</span><span class="sxs-lookup"><span data-stu-id="f38f6-186">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f38f6-187">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="f38f6-187">Troubleshooting</span></span>

<span data-ttu-id="f38f6-188">[完成したアプリ](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations)をダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="f38f6-188">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="f38f6-189">アプリは次の例外を生成します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-189">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="f38f6-190">解決方法 : `dotnet ef database update` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f38f6-190">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="f38f6-191">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f38f6-191">Additional resources</span></span>

* [<span data-ttu-id="f38f6-192">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="f38f6-192">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="f38f6-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="f38f6-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="f38f6-194">パッケージ マネージャー コンソール (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="f38f6-194">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="f38f6-195">[前へ](xref:data/ef-rp/sort-filter-page)
> [次へ](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="f38f6-195">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>
