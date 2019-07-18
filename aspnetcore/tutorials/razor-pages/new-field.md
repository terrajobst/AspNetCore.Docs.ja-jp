---
title: ASP.NET Core で Razor ページに新しいフィールドを追加する
author: rick-anderson
description: Entity Framework Core を使用した Razor ページへの新しいフィールドの追加方法
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 904207ed775cc689c36953c29d202788580d8f60
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815307"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>ASP.NET Core で Razor ページに新しいフィールドを追加する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。

* モデルに新しいフィールドを追加する。
* 新しいフィールド スキーマの変更をデータベースに移行する。

EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。

* テーブルをデータベースに追加し、データベースのスキーマが生成元のモデル クラスと同期しているかを追跡します。
* モデル クラスがデータベースと同期されていない場合、EF は例外をスローします。

同期中のスキーマ/モデルが自動的に検証されるようにすると、整合性のないデータベース/コードの問題を発見しやすくなります。

## <a name="adding-a-rating-property-to-the-movie-model"></a>ムービー モデルへの評価プロパティの追加

*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

アプリをビルドします。

*Pages/Movies/Index.cshtml* を編集し、`Rating` フィールドを追加します。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

次のページを更新します。

* [削除] と [詳細] ページに、`Rating` フィールドを追加します。
* [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) を `Rating` フィールドを使用して更新します。
* [編集] ページに、`Rating` フィールドを追加します。

DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。 ここで実行すると、アプリによって `SqlException` がスローされます。

`SqlException: Invalid column name 'Rating'.`

このエラーが表示されるのは、更新された Movie モデル クラスがデータベースの Movie テーブルのスキーマと異なるためです。 (データベース テーブルに `Rating` 列はありません)。

このエラーを解決するための手法がいくつかあります。

1. Entity Framework に、新しいモデル クラス スキーマを使用してデータベースを自動的にドロップさせ、再作成させます。 この手法は、開発周期の早い段階で便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。 これの欠点は、データベースで既存のデータが失われることです。 実稼働データベースでこの方法は使用しないでください。 スキーマの変更時に DB を削除し、初期化子を使用して、データベースにテスト データを自動的にシードすることは、多くの場合、アプリケーションを開発する上で有益な方法となります。

2. モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。 この手法の長所は、データが維持されることです。 この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。

3. Code First Migrations を使用して、データベース スキーマを更新します。

このチュートリアルでは、Code First Migrations を利用します。

新しい列に値を提供するように、`SeedData` クラスを更新します。 下に変更のサンプルがありますが、`new Movie` ブロックごとにこの変更を行ってください。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[完成した SeedData.cs ファイル](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)を参照してください。

ソリューションをビルドします。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>評価フィールドの移行を追加する

**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。
PMC で、次のコマンドを入力します。

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` コマンドにより、フレームワークに次の指示があります。

* `Movie` モデルを、`Movie` DB のスキーマと比較します。
* DB スキーマを新しいモデルに移行するコードを作成します。

"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。 移行ファイルには意味のある名前を使用すると便利です。

フレームワークは、データベースにスキーマ変更を適用するように、`Update-Database` コマンドから指示されます。

<a name="ssox"></a>

DB 内のすべてのレコードを削除すると、初期化子は DB にデータを初期投入し、`Rating` フィールドを追加します。 これはブラウザーの削除リンクで行うか、[Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) から行うことができます。

別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。 SSOX 内でデータベースを削除するには:

* SSOX でデータベースを選択します。
* データベースを右クリックし、 *[削除]* を選択します。
* **[既存の接続を閉じる]** をチェックします。
* **[OK]** を選択します。
* [PMC](xref:tutorials/razor-pages/new-field#pmc) でデータベースを更新します。

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>データベースを削除して再作成する

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

データベースを削除し、移行を使ってデータベースを再作成します。 データベースを削除するには、データベース ファイル (*MvcMovie.db*) を削除します。 次に、`ef database update` コマンドを実行します。

```console
dotnet ef database update
```

---

アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。 データベースがシード処理されない場合は、`SeedData.Initialize` メソッドでブレークポイントを設定します。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [前へ:検索の追加](xref:tutorials/razor-pages/search)
> [次へ: 検証の追加](xref:tutorials/razor-pages/validation)
