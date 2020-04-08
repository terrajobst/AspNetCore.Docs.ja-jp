---
title: ASP.NET Core の Razor ページと EF Core - 移行 - 4/8
author: rick-anderson
description: このチュートリアルでは、ASP.NET Core MVC アプリでデータ モデルの変更を管理するための EF Core の移行機能の使用を開始します。
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 86fd83c898fce8e121e4d259aaca12c59591e606
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78645554"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a>ASP.NET Core の Razor ページと EF Core - 移行 - 4/8

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能を紹介します。

新しいアプリを開発するときには、頻繁にデータ モデルを変更します。 モデルを変更するたびに、モデルはデータベースと同期されなくなります。 このチュートリアル シリーズでは、最初にデータベースが存在しない場合に、データベースを作成するように Entity Framework を構成します。 データ モデルが変更されるたびに、データベースを削除する必要があります。 次にアプリが実行されると、`EnsureCreated` への呼び出しによって、新しいデータ モデルに一致するデータベースが再作成されます。 その後、`DbInitializer` クラスが実行され、新しいデータベースがシードされます。

このアプローチは、実稼働環境にアプリを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。 アプリが実稼働環境で実行されているときには、通常は維持する必要があるデータをアプリが格納します。 変更 (新しい列の追加など) が加えられるたびに、テスト データベースを使用してアプリを開始することはできません。 EF Core の移行機能では、新しいデータベースを作成する代わりに、EF Core でデータベース スキーマを更新できるようにすることで、この問題を解決します。

データ モデルが変更されたときにデータベースを削除して再作成する代わりに、移行によってスキーマを更新し、既存のデータを維持します。

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>データベースを削除する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**SQL Server オブジェクトエクスプローラー** (SSOX) を使用してデータベースを削除するか、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* EF CLI をインストールするには、コマンド プロンプトで次のコマンドを実行します。

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* コマンド プロンプトで、プロジェクト フォルダーに移動します。 プロジェクト フォルダーには、*ContosoUniversity.csproj* ファイルが含まれています。

* *CU.db* ファイルを削除するか、次のコマンドを実行します。

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>初期移行を作成する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC で次のコマンドを実行します。

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

コマンド プロンプトがプロジェクト フォルダーにあることを確認し、次のコマンドを実行します。

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Up メソッドと Down メソッド

EF Core の `migrations add` コマンドでは、データベースを作成するコードが生成されました。 この移行コードは *Migrations\<timestamp>_InitialCreate.cs* ファイルにあります。 `Up` クラスの `InitialCreate` メソッドでは、データ モデルのエンティティ セットに対応するデータベース テーブルが作成されます。 次の例で示すように、`Down` メソッドは、それらを削除します。

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

上記のコードは、初期の移行のためのコードです。 コード:

* `migrations add InitialCreate` コマンドによって生成されました。 
* `database update` コマンドによって実行されます。
* データベース コンテキスト クラスによって指定されたデータ モデル用のデータベースを作成します。

移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用されます。 移行名には、任意の有効なファイル名を指定できます。 移行で行われている処理をまとめた単語または語句を選択することをお勧めします。 たとえば、department テーブルを追加する移行に "AddDepartmentTable" という名前を付けることができます。

## <a name="the-migrations-history-table"></a>移行履歴テーブル

* SSOX または SQLite ツールを使用してデータベースを検査します。
* `__EFMigrationsHistory` テーブルが追加されていることに注意してください。 `__EFMigrationsHistory` テーブルは、どの移行がデータベースに適用されたかを追跡します。
* `__EFMigrationsHistory` テーブル内のデータを表示します。 最初の移行に対する 1 行が表示されます。

## <a name="the-data-model-snapshot"></a>データ モデルのスナップショット

移行によって、現在のデータ モデルの*スナップショット*が *Migrations/SchoolContextModelSnapshot.cs* 内に作成されます。 移行を追加するときに、EF では、スナップショット ファイルと現在のデータ モデルを比較することによって変更内容を判断します。

スナップショット ファイルではデータ モデルの状態が追跡されるため、`<timestamp>_<migrationname>.cs` ファイルを削除することによって移行を削除することはできません。 最新の移行を取り消すには、`migrations remove` コマンドを使用する必要があります。 このコマンドでは移行が削除され、スナップショットが正しく確実にリセットされます。 詳細については、「[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)」を参照してください。

## <a name="remove-ensurecreated"></a>Remove EnsureCreated

このチュートリアル シリーズは、`EnsureCreated` を使用して開始されました。 `EnsureCreated` では、移行履歴テーブルが作成されないため、移行に使用することはできません。 これは、データベースが頻繁に削除および再作成されるようなテストや迅速なプロトタイプのために設計されています。

これ以降、チュートリアルでは移行が使用されます。

*Data/DBInitializer.cs* で、次の行をコメント アウトします。

```csharp
context.Database.EnsureCreated();
```
アプリを実行し、データベースがシードされていることを確認します。

## <a name="applying-migrations-in-production"></a>運用環境で移行を適用する

実稼働アプリでは、アプリケーションの起動時に **Database.Migrate** を呼び出さ[ない](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)ことをお勧めします。 `Migrate` は、サーバー ファームに展開されているアプリから呼び出さないでください。 アプリが複数のサーバー インスタンスにスケールアウトされている場合は、確実にデータベース スキーマの更新が複数のサーバーから行われたり、読み取り/書き込みアクセスと競合したりしないようにするのは困難です。

データベースの移行は、展開の一部として制御された方法で行う必要があります。 実稼働データベースの移行には次の方法があります。

* 移行を使用して SQL スクリプトを作成し、展開で SQL スクリプトを使用します。
* 制御された環境から `dotnet ef database update` を実行します。

## <a name="troubleshooting"></a>トラブルシューティング

アプリで SQL Server LocalDB を使用し、次の例外が表示される場合:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

このソリューションでは、コマンド プロンプトで `dotnet ef database update` を実行する可能性があります。

### <a name="additional-resources"></a>その他の技術情報

* [EF Core CLI](/ef/core/miscellaneous/cli/dotnet)
* [パッケージ マネージャー コンソール (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>次の手順

次のチュートリアルでは、エンティティのプロパティと新しいエンティティを追加して、データ モデルを構築します。

> [!div class="step-by-step"]
> [前のチュートリアル](xref:data/ef-rp/sort-filter-page)
> [次のチュートリアル](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能を使用します。

解決できない問題が発生した場合は、[完成したアプリ](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)をダウンロードしてください。

新しいアプリを開発するときには、頻繁にデータ モデルを変更します。 モデルを変更するたびに、モデルはデータベースと同期されなくなります。 このチュートリアルでは、最初にデータベースが存在しない場合にデータベースを作成するように Entity Framework を構成します。 データ モデルが変更されるたびに次の処理を実行します。

* データベースが削除されます。
* EF がモデルと一致する新しいデータベースを作成します。
* アプリがテスト データを DB に格納します。

このアプローチは、実稼働環境にアプリを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。 アプリが実稼働環境で実行されているときには、通常は維持する必要があるデータをアプリが格納します。 変更 (新しい列の追加など) が加えられるたびにテスト データベースを使用してアプリを開始することはできません。 EF コア移行機能は、新しいデータベースを作成する代わりに EF Core でデータベース スキーマを更新できるようにすることでこの問題を解決します。

データ モデルが変更されたときにデータベースを削除して再作成する代わりに、移行によってスキーマを更新し、既存のデータを維持します。

## <a name="drop-the-database"></a>データベースを削除する

**SQL Server オブジェクト エクスプローラー** (SSOX) または `database drop` コマンドを使用します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**パッケージ マネージャー コンソール** (PMC) で、次のコマンドを実行します。

```powershell
Drop-Database
```

PMC から `Get-Help about_EntityFrameworkCore` を実行してヘルプ情報を入手します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。 プロジェクト フォルダーには *Startup.cs* ファイルが含まれます。

コマンド ウィンドウで次のコマンドを入力します。

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>初期移行を作成し、DB を更新する

プロジェクトをビルドし、最初の移行を作成します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Up および Down メソッドを確認する

EF Core コマンド `migrations add` で、DB の作成元のコードが生成されました。 この移行コードは *Migrations\<timestamp>_InitialCreate.cs* ファイルにあります。 `Up` クラスの `InitialCreate` メソッドは、データ モデルのエンティティ セットに対応するデータベース テーブルを作成します。 次の例で示すように、`Down` メソッドは、それらを削除します。

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

移行は、`Up` メソッドを呼び出して、移行のためのデータ モデルの変更を実装します。 更新をロールバックするためのコマンドを入力すると、移行が `Down` メソッドを呼び出します。

上記のコードは、初期の移行のためのコードです。 そのコードは、`migrations add InitialCreate` コマンドが実行されたときに作成されます。 移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用されます。 移行名には、任意の有効なファイル名を指定できます。 移行で行われている処理をまとめた単語または語句を選択することをお勧めします。 たとえば、department テーブルを追加する移行に "AddDepartmentTable" という名前を付けることができます。

最初の移行が作成され、データベースが存在している場合:

* データベース作成コードが生成されます。
* データベースは既にデータ モデルと一致しているため、データベース作成コードを実行する必要はありません。 データベース作成コードが実行された場合、データベースは既にデータ モデルと一致しているため、何も変更しません。

新しい環境にアプリが展開されるときには、データベースを作成するためのデータベース作成コードを実行する必要があります。

以前は DB が削除されて存在しないため、移行によって新しい DB が作成されていました。

### <a name="the-data-model-snapshot"></a>データ モデルのスナップショット

移行は、現在のデータベース スキーマの*スナップショット*を *Migrations/SchoolContextModelSnapshot.cs* 内に作成します。 移行を追加するときに、EF は、スナップショット ファイルとデータ モデルを比較することによって変更内容を判断します。

移行を削除するには、次のコマンドを使用します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove-Migration

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

詳細については、「[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)」を参照してください。

---

remove migrations コマンドによって移行が削除され、スナップショットが正しくリセットされたことが確認されます。

### <a name="remove-ensurecreated-and-test-the-app"></a>EnsureCreated を削除し、アプリをテストする

初期の開発では、`EnsureCreated` が使用されました。 このチュートリアルでは、移行を使用します。 `EnsureCreated` には次の制限が適用されます。

* 移行をバイパスし、データベースとスキーマを作成します。
* 移行テーブルは作成しません。
* 移行と共に使用することは*できません*。
* データベースが頻繁に削除および再作成されるようなテストや迅速なプロトタイプのために設計されています。

`EnsureCreated` を削除します。

```csharp
context.Database.EnsureCreated();
```

アプリを実行し、DB がシードされていることを確認します。

### <a name="inspect-the-database"></a>データベースを検査する

**SQL Server オブジェクト エクスプローラー**を使用してデータベースを検査します。 `__EFMigrationsHistory` テーブルが追加されていることに注意してください。 `__EFMigrationsHistory` テーブルは、どの移行がデータベースに適用されたかを追跡します。 `__EFMigrationsHistory` テーブルのデータを表示すると、最初の移行の 1 つの行が表示されます。 前の CLI の出力例の最後のログは、この行を作成する INSERT ステートメントを示しています。

アプリを実行して、すべてが適切に機能していることを確認します。

## <a name="applying-migrations-in-production"></a>運用環境で移行を適用する

実稼働アプリケーションでは、アプリケーションの起動時に **Database.Migrate** を[呼び出さない](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)ことをお勧めします。 `Migrate` をサーバー ファームのアプリから呼び出すことはできません。 たとえば、アプリがスケールアウト (アプリの複数のインスタンスを実行する) を使用してクラウドに展開されている場合があります。

データベースの移行は、展開の一部として制御された方法で行う必要があります。 実稼働データベースの移行には次の方法があります。

* 移行を使用して SQL スクリプトを作成し、展開で SQL スクリプトを使用します。
* 制御された環境から `dotnet ef database update` を実行します。

EF Core は、`__MigrationsHistory` テーブルを使用して、移行を実行する必要があるかどうかを確認します。 データベースが最新の状態になっている場合、移行は実行されません。

## <a name="troubleshooting"></a>トラブルシューティング

[完成したアプリ](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)をダウンロードします。

アプリは次の例外を生成します。

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Solution: Run `dotnet ef database update`

### <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。
* [パッケージ マネージャー コンソール (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [前へ](xref:data/ef-rp/sort-filter-page)
> [次へ](xref:data/ef-rp/complex-data-model)

::: moniker-end

