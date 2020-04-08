---
title: データベースと ASP.NET Core を使用する
author: rick-anderson
description: データベースと ASP.NET Core の使用について説明します。
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649514"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="aeb3c-103">データベースと ASP.NET Core を使用する</span><span class="sxs-lookup"><span data-stu-id="aeb3c-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="aeb3c-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="aeb3c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="aeb3c-105">`RazorPagesMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="aeb3c-106">データベース コンテキストは、[Startup.cs](xref:fundamentals/dependency-injection) の `ConfigureServices` メソッドで*依存性の注入*コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-108">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="aeb3c-109">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="aeb3c-110">ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aeb3c-112">データベースの名前の値は (`Database={Database name}`) ユーザーが生成したコードでは異なります。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="aeb3c-113">名前の値は任意です。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-114">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="aeb3c-115">アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列を実際のデータベース サーバーに設定できます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="aeb3c-116">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="aeb3c-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="aeb3c-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="aeb3c-119">LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="aeb3c-120">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="aeb3c-121">LocalDB データベースでは、既定で `*.mdf` ディレクトリに `C:\Users\<user>\` ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="aeb3c-122">**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![[View] メニュー](sql/_static/ssox.png)

* <span data-ttu-id="aeb3c-124">`Movie` テーブルを右クリックし、 **[デザイナーの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie テーブルに対して開かれたコンテキスト メニュー](sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](sql/_static/dv.png)

<span data-ttu-id="aeb3c-127">`ID` の横のキー アイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="aeb3c-128">既定では、EF で主キーに `ID` という名前のプロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="aeb3c-129">`Movie` テーブルを右クリックし、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![開いた Movie テーブルにテーブル データが表示されています](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-131">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="aeb3c-132">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="aeb3c-132">Seed the database</span></span>

<span data-ttu-id="aeb3c-133">次のコードを使用して、`SeedData`Models*フォルダーに* という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="aeb3c-134">DB にムービーがある場合、シード初期化子が返され、ムービーは追加されません。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="aeb3c-135">シード初期化子の追加</span><span class="sxs-lookup"><span data-stu-id="aeb3c-135">Add the seed initializer</span></span>

<span data-ttu-id="aeb3c-136">*Program.cs* で、次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="aeb3c-137">依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="aeb3c-138">seed メソッドを呼び出し、コンテキストを渡します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="aeb3c-139">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="aeb3c-140">次は、更新された *Program.cs* ファイルのコードです。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="aeb3c-141">`Update-Database` が実行されていなかった場合、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="aeb3c-142">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="aeb3c-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aeb3c-144">DB 内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-144">Delete all the records in the DB.</span></span> <span data-ttu-id="aeb3c-145">これはブラウザーの削除リンクで行うか、[SSOX](xref:tutorials/razor-pages/new-field#ssox) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="aeb3c-146">アプリを強制的に初期化して (`Startup` クラスでメソッドを呼び出す)、シード メソッドが実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="aeb3c-147">強制的に初期化するには、IIS Express を停止してから再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="aeb3c-148">これは次の方法のいずれかを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="aeb3c-149">通知領域の IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express システム トレイ アイコン](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](sql/_static/stopIIS.png)

    * <span data-ttu-id="aeb3c-152">非デバッグ モードで VS を実行していた場合は、F5 キーを押してデバッグ モードで実行します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="aeb3c-153">デバッグ モードで VS を実行していた場合は、デバッガーを停止して、F5 キーを押します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-154">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="aeb3c-155">DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="aeb3c-156">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="aeb3c-157">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="aeb3c-158">次のチュートリアルでは、データの表示が改善される予定です。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aeb3c-159">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aeb3c-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="aeb3c-160">[前: スキャフォールディングされた Razor ページ](xref:tutorials/razor-pages/page)
> [次: ページの更新](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="aeb3c-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="aeb3c-161">`RazorPagesMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="aeb3c-162">データベース コンテキストは、[Startup.cs](xref:fundamentals/dependency-injection) の `ConfigureServices` メソッドで*依存性の注入*コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-164">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="aeb3c-165">`ConfigureServices` で使用されているメソッドの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="aeb3c-166">[ASP.NET Core での ](xref:security/gdpr) 用の EU の一般データ保護規制 (GDPR) のサポート`CookiePolicyOptions`</span><span class="sxs-lookup"><span data-stu-id="aeb3c-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="aeb3c-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="aeb3c-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="aeb3c-168">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="aeb3c-169">ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aeb3c-171">データベースの名前の値は (`Database={Database name}`) ユーザーが生成したコードでは異なります。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="aeb3c-172">名前の値は任意です。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aeb3c-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aeb3c-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-174">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="aeb3c-175">アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列を実際のデータベース サーバーに設定できます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="aeb3c-176">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="aeb3c-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="aeb3c-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="aeb3c-179">LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="aeb3c-180">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="aeb3c-181">LocalDB データベースでは、既定で `*.mdf` ディレクトリに `C:/Users/<user/>` ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="aeb3c-182">**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![[View] メニュー](sql/_static/ssox.png)

* <span data-ttu-id="aeb3c-184">`Movie` テーブルを右クリックし、 **[デザイナーの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie テーブルで開かれたコンテキスト メニュー](sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](sql/_static/dv.png)

<span data-ttu-id="aeb3c-187">`ID` の横のキー アイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="aeb3c-188">既定では、EF で主キーに `ID` という名前のプロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="aeb3c-189">`Movie` テーブルを右クリックし、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![開いた Movie テーブルにテーブル データが表示されています](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="aeb3c-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aeb3c-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-192">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="aeb3c-193">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="aeb3c-193">Seed the database</span></span>

<span data-ttu-id="aeb3c-194">次のコードを使用して、`SeedData`Models*フォルダーに* という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="aeb3c-195">DB にムービーがある場合、シード初期化子が返され、ムービーは追加されません。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="aeb3c-196">シード初期化子の追加</span><span class="sxs-lookup"><span data-stu-id="aeb3c-196">Add the seed initializer</span></span>

<span data-ttu-id="aeb3c-197">*Program.cs* で、次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="aeb3c-198">依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="aeb3c-199">seed メソッドを呼び出し、コンテキストを渡します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="aeb3c-200">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="aeb3c-201">次は、更新された *Program.cs* ファイルのコードです。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="aeb3c-202">運用アプリは `Database.Migrate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="aeb3c-203">これは、`Update-Database` が実行されていないとき、前述のコードに追加され、次の例外を阻止します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="aeb3c-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.\(SqlException: ログインで要求されている "RazorPagesMovieContext-21" データベースを開くことができませんでした。\)</span><span class="sxs-lookup"><span data-stu-id="aeb3c-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="aeb3c-205">The login failed.\(ログインに失敗しました。\)</span><span class="sxs-lookup"><span data-stu-id="aeb3c-205">The login failed.</span></span>
<span data-ttu-id="aeb3c-206">Login failed for user 'user name'.\(ユーザー 'ユーザー名' はログインできませんでした。\)</span><span class="sxs-lookup"><span data-stu-id="aeb3c-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="aeb3c-207">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="aeb3c-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aeb3c-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb3c-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aeb3c-209">DB 内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-209">Delete all the records in the DB.</span></span> <span data-ttu-id="aeb3c-210">これはブラウザーの削除リンクで行うか、[SSOX](xref:tutorials/razor-pages/new-field#ssox) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="aeb3c-211">アプリを強制的に初期化して (`Startup` クラスでメソッドを呼び出す)、シード メソッドが実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="aeb3c-212">強制的に初期化するには、IIS Express を停止してから再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="aeb3c-213">これは次の方法のいずれかを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="aeb3c-214">通知領域にある IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express システム トレイ アイコン](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](sql/_static/stopIIS.png)

    * <span data-ttu-id="aeb3c-217">非デバッグ モードで VS を実行していた場合は、F5 キーを押してデバッグ モードで実行します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="aeb3c-218">デバッグ モードで VS を実行していた場合は、デバッガーを停止して、F5 キーを押します。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aeb3c-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aeb3c-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aeb3c-220">DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="aeb3c-221">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="aeb3c-222">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aeb3c-223">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aeb3c-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aeb3c-224">DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="aeb3c-225">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="aeb3c-226">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="aeb3c-227">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-227">The app shows the seeded data:</span></span>

![ムービー データが表示された、Chrome で開かれているムービー アプリケーション](sql/_static/m55.png)

<span data-ttu-id="aeb3c-229">次のチュートリアルでは、データの表示をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="aeb3c-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aeb3c-230">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aeb3c-230">Additional resources</span></span>

* [<span data-ttu-id="aeb3c-231">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="aeb3c-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="aeb3c-232">[前: スキャフォールディングされた Razor ページ](xref:tutorials/razor-pages/page)
> [次: ページの更新](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="aeb3c-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
