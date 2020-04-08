---
title: ASP.NET Core MVC アプリでの SQL の操作
author: rick-anderson
description: ASP.NET Core MVC アプリ内で SQL Server LocalDB または SQLite を使用する方法について説明します。
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649808"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="d8e85-103">ASP.NET Core での SQL の操作</span><span class="sxs-lookup"><span data-stu-id="d8e85-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8e85-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8e85-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8e85-105">`MvcMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d8e85-106">データベース コンテキストは、[Startup.cs](xref:fundamentals/dependency-injection) ファイルの `ConfigureServices` メソッドで*依存性の注入*コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e85-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e85-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="d8e85-108">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="d8e85-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8e85-109">ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e85-110">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d8e85-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="d8e85-111">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="d8e85-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8e85-112">ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="d8e85-113">アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列を運用 SQL Server に設定できます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="d8e85-114">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8e85-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e85-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e85-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d8e85-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="d8e85-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d8e85-117">LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。</span><span class="sxs-lookup"><span data-stu-id="d8e85-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="d8e85-118">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="d8e85-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d8e85-119">既定では、LocalDB データベースによって *C:/Users/{user}* ディレクトリに *.mdf* ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="d8e85-120">**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![[View] メニュー](working-with-sql/_static/ssox.png)

* <span data-ttu-id="d8e85-122">`Movie` テーブルを右クリックして **[デザイナーの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Movie テーブルで開かれたコンテキスト メニュー](working-with-sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](working-with-sql/_static/dv.png)

<span data-ttu-id="d8e85-125">`ID` の横のキー アイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d8e85-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d8e85-126">既定では、EF は `ID` という名前のプロパティを主キーにします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="d8e85-127">`Movie` テーブルを右クリックして **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Movie テーブルで開かれたコンテキスト メニュー](working-with-sql/_static/ssox2.png)

  ![開いた Movie テーブルにテーブル データが表示されています](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e85-130">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d8e85-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="d8e85-131">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="d8e85-131">Seed the database</span></span>

<span data-ttu-id="d8e85-132">`SeedData`Models*フォルダーに* という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="d8e85-133">生成されたコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d8e85-134">DB にムービーがある場合、シード初期化子が返され、ムービーは追加されません。</span><span class="sxs-lookup"><span data-stu-id="d8e85-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d8e85-135">シード初期化子の追加</span><span class="sxs-lookup"><span data-stu-id="d8e85-135">Add the seed initializer</span></span>

<span data-ttu-id="d8e85-136">*Program.cs* の内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="d8e85-137">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="d8e85-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e85-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e85-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8e85-139">DB 内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-139">Delete all the records in the DB.</span></span> <span data-ttu-id="d8e85-140">これはブラウザーの削除リンクで行うか、SSOX から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="d8e85-141">アプリを強制的に初期化して (`Startup` クラスでメソッドを呼び出す)、シード メソッドが実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d8e85-142">強制的に初期化するには、IIS Express を停止してから再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8e85-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d8e85-143">これは次の方法のいずれかを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d8e85-144">通知領域の IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express システム トレイ アイコン](working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="d8e85-147">非デバッグ モードで VS を実行していた場合は、F5 キーを押してデバッグ モードで実行します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="d8e85-148">デバッグ モードで VS を実行していた場合は、デバッガーを停止して、F5 キーを押します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e85-149">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d8e85-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d8e85-150">DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="d8e85-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d8e85-151">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="d8e85-152">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-152">The app shows the seeded data.</span></span>

![ムービー データが表示された、Microsoft Edge で開かれている MVC ムービー アプリケーション](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="d8e85-154">[前へ](adding-model.md)
> [次へ](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="d8e85-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8e85-155">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8e85-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8e85-156">`MvcMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d8e85-157">データベース コンテキストは、[Startup.cs](xref:fundamentals/dependency-injection) ファイルの `ConfigureServices` メソッドで*依存性の注入*コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e85-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e85-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="d8e85-159">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="d8e85-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8e85-160">ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e85-161">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d8e85-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="d8e85-162">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="d8e85-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8e85-163">ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="d8e85-164">テストまたは実稼働サーバーにアプリを配置する場合は、環境変数または別の方法を使用して、実際の SQL Server に接続文字列を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="d8e85-165">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8e85-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e85-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e85-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d8e85-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="d8e85-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d8e85-168">LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。</span><span class="sxs-lookup"><span data-stu-id="d8e85-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="d8e85-169">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="d8e85-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d8e85-170">既定では、LocalDB データベースによって *C:/Users/{user}* ディレクトリに *.mdf* ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="d8e85-171">**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![[View] メニュー](working-with-sql/_static/ssox.png)

* <span data-ttu-id="d8e85-173">`Movie` テーブルを右クリックして **[デザイナーの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Movie テーブルで開かれたコンテキスト メニュー](working-with-sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](working-with-sql/_static/dv.png)

<span data-ttu-id="d8e85-176">`ID` の横のキー アイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d8e85-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d8e85-177">既定では、EF は `ID` という名前のプロパティを主キーにします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="d8e85-178">`Movie` テーブルを右クリックして **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Movie テーブルで開かれたコンテキスト メニュー](working-with-sql/_static/ssox2.png)

  ![開いた Movie テーブルにテーブル データが表示されています](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e85-181">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d8e85-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="d8e85-182">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="d8e85-182">Seed the database</span></span>

<span data-ttu-id="d8e85-183">`SeedData`Models*フォルダーに* という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="d8e85-184">生成されたコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d8e85-185">DB にムービーがある場合、シード初期化子が返され、ムービーは追加されません。</span><span class="sxs-lookup"><span data-stu-id="d8e85-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d8e85-186">シード初期化子の追加</span><span class="sxs-lookup"><span data-stu-id="d8e85-186">Add the seed initializer</span></span>

<span data-ttu-id="d8e85-187">*Program.cs* の内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="d8e85-188">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="d8e85-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e85-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e85-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8e85-190">DB 内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-190">Delete all the records in the DB.</span></span> <span data-ttu-id="d8e85-191">これはブラウザーの削除リンクで行うか、SSOX から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="d8e85-192">アプリを強制的に初期化して (`Startup` クラスでメソッドを呼び出す)、シード メソッドが実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d8e85-193">強制的に初期化するには、IIS Express を停止してから再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8e85-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d8e85-194">これは次の方法のいずれかを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d8e85-195">通知領域の IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express システム トレイ アイコン](working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="d8e85-198">非デバッグ モードで VS を実行していた場合は、F5 キーを押してデバッグ モードで実行します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="d8e85-199">デバッグ モードで VS を実行していた場合は、デバッガーを停止して、F5 キーを押します。</span><span class="sxs-lookup"><span data-stu-id="d8e85-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e85-200">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d8e85-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d8e85-201">DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="d8e85-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d8e85-202">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="d8e85-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="d8e85-203">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d8e85-203">The app shows the seeded data.</span></span>

![ムービー データが表示された、Microsoft Edge で開かれている MVC ムービー アプリケーション](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="d8e85-205">[前へ](adding-model.md)
> [次へ](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="d8e85-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
