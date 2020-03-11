---
title: データベースと ASP.NET Core を使用する
author: rick-anderson
description: データベースと ASP.NET Core の使用について説明します。
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649514"
---
# <a name="work-with-a-database-and-aspnet-core"></a>データベースと ASP.NET Core を使用する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。 データベース コンテキストは、*Startup.cs* の `ConfigureServices` メソッドで[依存性の注入](xref:fundamentals/dependency-injection)コンテナーに登録されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。 ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

データベースの名前の値は (`Database={Database name}`) ユーザーが生成したコードでは異なります。 名前の値は任意です。

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列を実際のデータベース サーバーに設定できます。 詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。 LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。 LocalDB データベースでは、既定で `C:\Users\<user>\` ディレクトリに `*.mdf` ファイルが作成されます。

<a name="ssox"></a>
* **[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。

  ![[View] メニュー](sql/_static/ssox.png)

* `Movie` テーブルを右クリックし、 **[デザイナーの表示]** を選択します。

  ![Movie テーブルに対して開かれたコンテキスト メニュー](sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](sql/_static/dv.png)

`ID` の横のキー アイコンに注意してください。 既定では、EF で主キーに `ID` という名前のプロパティが作成されます。

* `Movie` テーブルを右クリックし、 **[データの表示]** を選択します。

  ![開いた Movie テーブルにテーブル データが表示されています](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>データベースのシード

次のコードを使用して、*Models* フォルダーに `SeedData` という名前の新しいクラスを作成します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

DB にムービーがある場合、シード初期化子が返され、ムービーは追加されません。

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>シード初期化子の追加

*Program.cs* で、次を実行するように `Main` メソッドを変更します。

* 依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。
* seed メソッドを呼び出し、コンテキストを渡します。
* seed メソッドが完了したら、コンテキストを破棄します。

次は、更新された *Program.cs* ファイルのコードです。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

`Update-Database` が実行されていなかった場合、次の例外が発生します。

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>アプリのテスト

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB 内のすべてのレコードを削除します。 これはブラウザーの削除リンクで行うか、[SSOX](xref:tutorials/razor-pages/new-field#ssox) から行うことができます。
* アプリを強制的に初期化して (`Startup` クラスでメソッドを呼び出す)、シード メソッドが実行されるようにします。 強制的に初期化するには、IIS Express を停止してから再起動する必要があります。 これは次の方法のいずれかを使用して行うことができます。

  * 通知領域の IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。

    ![IIS Express システム トレイ アイコン](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](sql/_static/stopIIS.png)

    * 非デバッグ モードで VS を実行していた場合は、F5 キーを押してデバッグ モードで実行します。
    * デバッグ モードで VS を実行していた場合は、デバッガーを停止して、F5 キーを押します。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。 アプリを停止および起動して、データベースをシードします。

アプリにシードされたデータが表示されます。

---

次のチュートリアルでは、データの表示が改善される予定です。

## <a name="additional-resources"></a>その他の技術情報

> [!div class="step-by-step"]
> [前へ:スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)
> [次:ページの更新](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。 データベース コンテキストは、*Startup.cs* の `ConfigureServices` メソッドで[依存性の注入](xref:fundamentals/dependency-injection)コンテナーに登録されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

`ConfigureServices` で使用されているメソッドの詳細については、以下を参照してください。

* [ASP.NET Core での `CookiePolicyOptions` 用の EU の一般データ保護規制 (GDPR) のサポート](xref:security/gdpr)
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。 ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

データベースの名前の値は (`Database={Database name}`) ユーザーが生成したコードでは異なります。 名前の値は任意です。

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列を実際のデータベース サーバーに設定できます。 詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。 LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。 LocalDB データベースでは、既定で `C:/Users/<user/>` ディレクトリに `*.mdf` ファイルが作成されます。

<a name="ssox"></a>
* **[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。

  ![[View] メニュー](sql/_static/ssox.png)

* `Movie` テーブルを右クリックし、 **[デザイナーの表示]** を選択します。

  ![Movie テーブルで開かれたコンテキスト メニュー](sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](sql/_static/dv.png)

`ID` の横のキー アイコンに注意してください。 既定では、EF で主キーに `ID` という名前のプロパティが作成されます。

* `Movie` テーブルを右クリックし、 **[データの表示]** を選択します。

  ![開いた Movie テーブルにテーブル データが表示されています](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>データベースのシード

次のコードを使用して、*Models* フォルダーに `SeedData` という名前の新しいクラスを作成します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

DB にムービーがある場合、シード初期化子が返され、ムービーは追加されません。

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>シード初期化子の追加

*Program.cs* で、次を実行するように `Main` メソッドを変更します。

* 依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。
* seed メソッドを呼び出し、コンテキストを渡します。
* seed メソッドが完了したら、コンテキストを破棄します。

次は、更新された *Program.cs* ファイルのコードです。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

運用アプリは `Database.Migrate` を呼び出しません。 これは、`Update-Database` が実行されていないとき、前述のコードに追加され、次の例外を阻止します。

SqlException:Cannot open database "RazorPagesMovieContext-21" requested by the login. (SqlException: ログインで要求されている "RazorPagesMovieContext-21" データベースを開くことができませんでした。) The login failed.\(ログインに失敗しました。\)
Login failed for user 'user name'.\(ユーザー 'ユーザー名' はログインできませんでした。\)

### <a name="test-the-app"></a>アプリのテスト

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB 内のすべてのレコードを削除します。 これはブラウザーの削除リンクで行うか、[SSOX](xref:tutorials/razor-pages/new-field#ssox) から行うことができます。
* アプリを強制的に初期化して (`Startup` クラスでメソッドを呼び出す)、シード メソッドが実行されるようにします。 強制的に初期化するには、IIS Express を停止してから再起動する必要があります。 これは次の方法のいずれかを使用して行うことができます。

  * 通知領域にある IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。

    ![IIS Express システム トレイ アイコン](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](sql/_static/stopIIS.png)

    * 非デバッグ モードで VS を実行していた場合は、F5 キーを押してデバッグ モードで実行します。
    * デバッグ モードで VS を実行していた場合は、デバッガーを停止して、F5 キーを押します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。 アプリを停止および起動して、データベースをシードします。

アプリにシードされたデータが表示されます。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。 アプリを停止および起動して、データベースをシードします。

アプリにシードされたデータが表示されます。

---

アプリにシードされたデータが表示されます。

![ムービー データが表示された、Chrome で開かれているムービー アプリケーション](sql/_static/m55.png)

次のチュートリアルでは、データの表示をクリーンアップします。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [前へ:スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)
> [次:ページの更新](xref:tutorials/razor-pages/da1)

::: moniker-end
