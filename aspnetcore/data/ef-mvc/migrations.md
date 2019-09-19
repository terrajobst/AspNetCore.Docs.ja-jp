---
title: 'チュートリアル: 移行機能の使用 - ASP.NET MVC と EF Core'
description: このチュートリアルでは、ASP.NET Core MVC アプリケーションでデータ モデルの変更を管理するための EF Core の移行機能の使用を開始します。
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 3ee95d9b648a90c90d06e33a30b568626a1eb0aa
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080830"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="5142b-103">チュートリアル: 移行機能の使用 - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="5142b-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="5142b-104">このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="5142b-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="5142b-105">チュートリアルの後半では、データ モデルを変更するときに移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="5142b-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="5142b-106">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="5142b-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5142b-107">移行について学習する</span><span class="sxs-lookup"><span data-stu-id="5142b-107">Learn about migrations</span></span>
> * <span data-ttu-id="5142b-108">接続文字列を変更する</span><span class="sxs-lookup"><span data-stu-id="5142b-108">Change the connection string</span></span>
> * <span data-ttu-id="5142b-109">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="5142b-109">Create an initial migration</span></span>
> * <span data-ttu-id="5142b-110">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="5142b-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="5142b-111">データ モデルのスナップショットについて学習する</span><span class="sxs-lookup"><span data-stu-id="5142b-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="5142b-112">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="5142b-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5142b-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5142b-113">Prerequisites</span></span>

* [<span data-ttu-id="5142b-114">並べ替え、フィルター処理、ページング</span><span class="sxs-lookup"><span data-stu-id="5142b-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="5142b-115">移行について</span><span class="sxs-lookup"><span data-stu-id="5142b-115">About migrations</span></span>

<span data-ttu-id="5142b-116">新しいアプリケーションを開発して、データ モデルが頻繁に変更される場合、モデルが変更されるたびに、モデルはデータベースと同期されなくなります。</span><span class="sxs-lookup"><span data-stu-id="5142b-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="5142b-117">データベースが存在しない場合にデータベースを作成するように Entity Framework を構成して、これらのチュートリアルを開始しました。</span><span class="sxs-lookup"><span data-stu-id="5142b-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="5142b-118">そのため、データ モデルを変更する (エンティティ クラスを追加、削除、または変更するか、DbContext クラスを変更する) たびに、データベースを削除することができ、EF でモデルに一致する新しいデータベースを作成し、テスト データを使ってシードを設定します。</span><span class="sxs-lookup"><span data-stu-id="5142b-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="5142b-119">このメソッドは、実稼働環境にアプリケーションを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="5142b-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="5142b-120">実稼働環境でアプリケーションを実行している場合、通常、保持する必要があるデータが保存され、新しい列の追加などの変更を加えるたびにすべてが失われないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5142b-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="5142b-121">EF Core の移行機能は、新しいデータベースを作成する代わりに、EF でデータベース スキーマを更新できるようにすることで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="5142b-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="5142b-122">移行を使用するには、**パッケージ マネージャー コンソール** (PMC) またはコマンド ライン インターフェイス (CLI) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5142b-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="5142b-123">このチュートリアルでは、CLI コマンドを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5142b-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="5142b-124">PMC については、[このチュートリアルの最後](#pmc)に説明します。</span><span class="sxs-lookup"><span data-stu-id="5142b-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="5142b-125">接続文字列を変更する</span><span class="sxs-lookup"><span data-stu-id="5142b-125">Change the connection string</span></span>

<span data-ttu-id="5142b-126">*appsettings.json* ファイルで、接続文字列のデータベース名を ContosoUniversity2 に変更するか、使用しているコンピューターではまだ使用していない別の名前に変更します。</span><span class="sxs-lookup"><span data-stu-id="5142b-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="5142b-127">この変更では、最初の移行で新しいデータベースが作成されるように、プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="5142b-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="5142b-128">これは移行の作業を開始するために必要ではありませんが、後でこの設定をお勧めする理由がわかります。</span><span class="sxs-lookup"><span data-stu-id="5142b-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="5142b-129">データベース名を変更する代わりに、データベースを削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="5142b-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="5142b-130">**SQL Server オブジェクト エクスプローラー** (SSOX) または `database drop` CLI コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5142b-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="5142b-131">次のセクションでは、CLI コマンドを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5142b-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="5142b-132">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="5142b-132">Create an initial migration</span></span>

<span data-ttu-id="5142b-133">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="5142b-133">Save your changes and build the project.</span></span> <span data-ttu-id="5142b-134">次に、コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="5142b-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="5142b-135">この操作を行う簡単な方法を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5142b-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="5142b-136">**[ソリューション エクスプローラー]** で、プロジェクトを右クリックし、コンテキスト メニューの **[エクスプローラーでフォルダーを開く]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="5142b-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![[エクスプローラーで開く] メニュー項目](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="5142b-138">アドレス バーに「cmd」と入力して、Enter キーを押します。</span><span class="sxs-lookup"><span data-stu-id="5142b-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![コマンド ウィンドウを開く](migrations/_static/open-command-window.png)

<span data-ttu-id="5142b-140">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="5142b-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="5142b-141">コマンド ウィンドウに次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5142b-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="5142b-142">" *"dotnet-ef" コマンドに一致する実行可能ファイルが見つかりませんでした*" というエラー メッセージが表示された場合、トラブルシューティングのヘルプについては、[このブログ記事](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5142b-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="5142b-143">"*別のプロセスによって使用されているため、ContosoUniversity.dll ファイルにアクセスできません。* " というエラー メッセージが表示された場合は、Windows のシステム トレイの IIS Express アイコンを見つけて右クリックし、 **[ContosoUniversity]、[サイトの停止]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="5142b-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="5142b-144">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="5142b-144">Examine Up and Down methods</span></span>

<span data-ttu-id="5142b-145">`migrations add` コマンドを実行したときに、EF では最初からデータベースを作成するコードが生成されています。</span><span class="sxs-lookup"><span data-stu-id="5142b-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="5142b-146">このコードは、 *\<タイムスタンプ>_InitialCreate.cs* という名前のファイルにある *[Migrations]* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="5142b-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="5142b-147">`InitialCreate` クラスの `Up` メソッドでは、データ モデルのエンティティ セットに対応するデータベース テーブルを作成し、`Down` メソッドでは、次の例に示すようにそれらを削除します。</span><span class="sxs-lookup"><span data-stu-id="5142b-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="5142b-148">移行は、`Up` メソッドを呼び出して、移行のためのデータ モデルの変更を実装します。</span><span class="sxs-lookup"><span data-stu-id="5142b-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="5142b-149">更新をロールバックするためのコマンドを入力すると、移行が `Down` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5142b-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="5142b-150">このコードは、`migrations add InitialCreate` コマンドを入力したときに作成された初期移行向けのものです。</span><span class="sxs-lookup"><span data-stu-id="5142b-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="5142b-151">移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用され、どのような名前でも付けることができます。</span><span class="sxs-lookup"><span data-stu-id="5142b-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="5142b-152">移行で行われている処理をまとめた単語または語句を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5142b-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="5142b-153">たとえば、後で移行に "AddDepartmentTable" という名前を付ける可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5142b-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="5142b-154">データベースが既に存在するときに、初期移行を作成した場合、データベースの作成コードが生成されますが、データベースは既にデータと一致しているため、作成コードを実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5142b-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="5142b-155">データベースがまだ存在しない別の環境にアプリを展開する場合、データベースを作成するために、このコードが実行されるため、最初にテストを行うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5142b-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="5142b-156">これが、移行で新しいデータベースを最初から作成できるように、前の接続文字列でデータベースの名前を変更した理由です。</span><span class="sxs-lookup"><span data-stu-id="5142b-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="5142b-157">データ モデルのスナップショット</span><span class="sxs-lookup"><span data-stu-id="5142b-157">The data model snapshot</span></span>

<span data-ttu-id="5142b-158">移行は、現在のデータベース スキーマの*スナップショット*を *Migrations/SchoolContextModelSnapshot.cs* 内に作成します。</span><span class="sxs-lookup"><span data-stu-id="5142b-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="5142b-159">移行を追加するときに、EF は、スナップショット ファイルとデータ モデルを比較することによって変更内容を判断します。</span><span class="sxs-lookup"><span data-stu-id="5142b-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="5142b-160">移行を削除するには、[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5142b-160">When deleting a migration, use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span> <span data-ttu-id="5142b-161">`dotnet ef migrations remove` によって移行が削除され、スナップショットが正しくリセットされたことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="5142b-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span>

<span data-ttu-id="5142b-162">スナップショット ファイルの使用方法の詳細については、[チーム環境での EF Core 移行](/ef/core/managing-schemas/migrations/teams)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5142b-162">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="5142b-163">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="5142b-163">Apply the migration</span></span>

<span data-ttu-id="5142b-164">コマンド ウィンドウで、次のコマンドを入力してデータベースとデータベース内のテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="5142b-164">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="5142b-165">コマンドからの出力は、データベースを設定する SQL コマンドのログを表示する以外は、`migrations add` コマンドと同様です。</span><span class="sxs-lookup"><span data-stu-id="5142b-165">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="5142b-166">次のサンプル出力では、ログのほとんどは省略されています。</span><span class="sxs-lookup"><span data-stu-id="5142b-166">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="5142b-167">ログ メッセージの詳細レベルを表示しない場合は、*appsettings.Development.json* ファイルでログ レベルを変更できます。</span><span class="sxs-lookup"><span data-stu-id="5142b-167">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="5142b-168">詳細については、<xref:fundamentals/logging/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5142b-168">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="5142b-169">**SQL Server オブジェクト エクスプローラー**を使用して、最初のチュートリアルで行ったように、データベースを調べます。</span><span class="sxs-lookup"><span data-stu-id="5142b-169">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="5142b-170">データベースに適用されている移行を記録する \_\_EFMigrationsHistory テーブルに追加があることがわかります。</span><span class="sxs-lookup"><span data-stu-id="5142b-170">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="5142b-171">テーブルのデータを表示すると、最初の移行の 1 行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5142b-171">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="5142b-172">(前の CLI の出力例の最後のログは、この行を作成する INSERT ステートメントを示しています)。</span><span class="sxs-lookup"><span data-stu-id="5142b-172">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="5142b-173">アプリケーションを実行して、すべてが以前と同じように動作することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5142b-173">Run the application to verify that everything still works the same as before.</span></span>

![Students インデックス ページ](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="5142b-175">CLI と PMC を比較する</span><span class="sxs-lookup"><span data-stu-id="5142b-175">Compare CLI and PMC</span></span>

<span data-ttu-id="5142b-176">移行を管理するための EF ツールは、.NET Core CLI コマンドから、または Visual Studio **パッケージ マネージャー コンソール** (PMC) ウィンドウの PowerShell コマンドレットから利用できます。</span><span class="sxs-lookup"><span data-stu-id="5142b-176">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="5142b-177">このチュートリアルでは、CLI の使用方法を示しますが、好みに応じて PMC を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5142b-177">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="5142b-178">PMC コマンドの EF コマンドは、[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) パッケージ内にあります。</span><span class="sxs-lookup"><span data-stu-id="5142b-178">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="5142b-179">このパッケージは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれているので、アプリに `Microsoft.AspNetCore.App` のパッケージ参照がある場合、パッケージ参照を追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5142b-179">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="5142b-180">**重要:** これは、 *.csproj* ファイルを編集して CLI 用にインストールするものと同じパッケージではありません。</span><span class="sxs-lookup"><span data-stu-id="5142b-180">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="5142b-181">`Tools.DotNet` で終わる CLI パッケージ名とは異なり、このパッケージの名前は `Tools` で終わります。</span><span class="sxs-lookup"><span data-stu-id="5142b-181">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="5142b-182">CLI コマンドの詳細については、「[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5142b-182">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="5142b-183">PMC コマンドの詳細については、「[パッケージ マネージャー コンソール (Visual Studio)](/ef/core/miscellaneous/cli/powershell)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5142b-183">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="5142b-184">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="5142b-184">Get the code</span></span>

[<span data-ttu-id="5142b-185">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="5142b-185">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="5142b-186">次のステップ</span><span class="sxs-lookup"><span data-stu-id="5142b-186">Next step</span></span>

<span data-ttu-id="5142b-187">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="5142b-187">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5142b-188">移行について学習した</span><span class="sxs-lookup"><span data-stu-id="5142b-188">Learned about migrations</span></span>
> * <span data-ttu-id="5142b-189">NuGet 移行パッケージについて学習した</span><span class="sxs-lookup"><span data-stu-id="5142b-189">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="5142b-190">接続文字列を変更した</span><span class="sxs-lookup"><span data-stu-id="5142b-190">Changed the connection string</span></span>
> * <span data-ttu-id="5142b-191">初期移行を作成した</span><span class="sxs-lookup"><span data-stu-id="5142b-191">Created an initial migration</span></span>
> * <span data-ttu-id="5142b-192">Up および Down メソッドを確認した</span><span class="sxs-lookup"><span data-stu-id="5142b-192">Examined Up and Down methods</span></span>
> * <span data-ttu-id="5142b-193">データ モデルのスナップショットについて学習した</span><span class="sxs-lookup"><span data-stu-id="5142b-193">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="5142b-194">移行を適用した</span><span class="sxs-lookup"><span data-stu-id="5142b-194">Applied the migration</span></span>

<span data-ttu-id="5142b-195">データ モデルの展開に関するより高度なトピックを確認するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="5142b-195">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="5142b-196">その途中で、追加の移行を作成して適用することになります。</span><span class="sxs-lookup"><span data-stu-id="5142b-196">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5142b-197">追加の移行を作成して適用する</span><span class="sxs-lookup"><span data-stu-id="5142b-197">Create and apply additional migrations</span></span>](complex-data-model.md)
