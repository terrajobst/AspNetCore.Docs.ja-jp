---
title: ASP.NET Core で razor ページの単体テスト
author: guardrex
description: Razor ページ アプリの単体テストを作成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f0e47f975579dc114eaeda375028ec62696f58ed
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394736"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core で razor ページの単体テスト

作成者: [Luke Latham](https://github.com/guardrex)

ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。 データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。

* Razor ページ アプリのパーツでは、アプリの構築時とは独立してとを単位としてまとめてを動作します。
* クラスとメソッドには、責任の範囲が制限されます。
* アプリの動作に関するその他のドキュメントが存在します。
* コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。

このトピックでは、Razor ページ アプリと単体テストの基本的な理解があることを前提としています。 Razor ページ アプリまたはテストの概念に詳しい場合は、次のトピックを参照してください。

* [Razor ページを始める](xref:razor-pages/index)
* [Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)
* [単体テスト c# dotnet テストと xUnit を使用して .NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプル プロジェクトは、2 つのアプリで構成されます。

| アプリ         | プロジェクト フォルダー                        | 説明 |
| ----------- | ------------------------------------- | ----------- |
| メッセージ アプリ | *src/RazorPagesTestSample*            | により、ユーザーの追加、削除のいずれか、すべてを削除、およびメッセージを分析できます。 |
| テスト アプリ    | *tests/RazorPagesTestSample.Tests*    | メッセージ アプリの単体テストに使用されます。データ アクセス層 (DAL) とインデックス ページのモデル。 |

などの IDE、組み込みのテスト機能を使用して、テストを実行できる[Visual Studio](https://visualstudio.microsoft.com)します。 使用して場合[Visual Studio Code](https://code.visualstudio.com/)またはコマンド プロンプトで次のコマンドを実行するコマンドライン、 *tests/RazorPagesTestSample.Tests*フォルダー。

```console
dotnet test
```

## <a name="message-app-organization"></a>メッセージ アプリ組織

メッセージ アプリでは、次の特性を持つ単純な Razor ページ メッセージ システムを示します。

* アプリのインデックス ページ (*Pages/Index.cshtml*と*Pages/Index.cshtml.cs*) UI とページ モデルのメソッドを追加、削除、およびメッセージ (メッセージあたりの平均の単語) の分析の制御を提供します.
* メッセージは、`Message`クラス (*Data/Message.cs*) 2 つのプロパティを持つ: `Id` (キー) と`Text`(メッセージ)。 `Text`プロパティは必須であり 200 文字に制限されます。
* 使用してメッセージを保存する[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;します。
* アプリのデータベース コンテキスト クラス内でデータ アクセス層 (DAL) に含まれる`AppDbContext`(*Data/AppDbContext.cs*)。 DAL メソッドをマーク`virtual`テストで使用されるメソッドのモックを作成することができます。
* データベースがアプリの起動時に空の場合、メッセージ ストアは、3 つのメッセージで初期化されます。 これら*シード処理されるメッセージ*テストでも使用されます。

&#8224;EF トピック[InMemory のテスト](/ef/core/miscellaneous/testing/in-memory)MSTest を使用したテストにメモリ内データベースを使用する方法について説明します。 このトピックでは、 [xUnit](https://xunit.github.io/)テスト フレームワーク。 テストの概念と別のテスト フレームワーク間でのテストの実装は似ていますが、同一ではないです。

アプリは、リポジトリ パターンを使用しないしの有効な例はありませんが、 [Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)、Razor ページは、これらのパターンの開発をサポートしています。 詳細については、次を参照してください。[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テスト コント ローラー ロジック](/aspnet/core/mvc/controllers/testing)(このサンプルは、リポジトリ パターンを実装)。

## <a name="test-app-organization"></a>組織のアプリをテストします。

テスト アプリが内部でコンソール アプリ、 *tests/RazorPagesTestSample.Tests*フォルダー。

| アプリ フォルダーのテスト | 説明 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* DAL の単体テストが含まれています。</li><li>*IndexPageTests.cs*インデックス ページのモデルの単体テストが含まれています。</li></ul> |
| *ユーティリティ*     | 含まれています、`TestDbContextOptions`メソッドを各テストのベースラインの状態にデータベースをリセットするために、各 DAL の単体テストのコンテキストのオプションを新しいデータベースに作成するために使用します。 |

テスト フレームワークは[xUnit](https://xunit.github.io/)します。 オブジェクトのモック作成フレームワークが[Moq](https://github.com/moq/moq4)します。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>単体テストのデータ アクセス層 (DAL)

メッセージ アプリに含まれている 4 つの方法で、DAL が、`AppDbContext`クラス (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。 各メソッドでは、テスト アプリで 1 つまたは 2 つの単体テストがあります。

| DAL メソッド               | 関数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 取得、`List<Message>`データベースを順に並べ替えてから、`Text`プロパティ。 |
| `AddMessageAsync`        | 追加、`Message`データベースにします。                                          |
| `DeleteAllMessagesAsync` | すべてを削除します`Message`データベースからのエントリ。                           |
| `DeleteMessageAsync`     | 1 つを削除します。`Message`によってデータベースから`Id`します。                      |

DAL の単体テストを必要と[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)新しいを作成するときに`AppDbContext`テストごとにします。 作成する方法、`DbContextOptions`は、各テストを使用する、 [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

このアプローチの問題は、各テストがどのような状態、前のテストが左でデータベースを受信します。 互いに干渉しないアトミック単位テストを作成しようとするとき、これは問題が発生します。 強制的に、`AppDbContext`テストごとに新しいデータベースのコンテキストを使用するを指定する、`DbContextOptions`新しいサービス プロバイダーに基づいているインスタンス。 テスト アプリを使用してこれを行う方法を示しています。 その`Utilities`クラス メソッド`TestDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*)。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

使用して、 `DbContextOptions` DAL 単体テストによって、新しいデータベース インスタンスにアトミックに実行するには、各テスト。

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

内の各テスト メソッド、`DataAccessLayerTest`クラス (*UnitTests/DataAccessLayerTest.cs*) 配置 Act アサートと同様のパターンに従います。

1. 配置します。データベースがテストの構成や、予想される結果が定義されています。
1. Act:テストを実行します。
1. アサートします。アサーションは、テスト結果が成功したかどうかを決定されます。

たとえば、`DeleteMessageAsync`で識別される 1 つのメッセージを削除するため、メソッドはその`Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*)。

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

このメソッドの 2 つのテストがあります。 メッセージがデータベースに存在する場合、メソッドはメッセージを削除します。 1 つのテストを確認します。 データベースを変更しないこと、その他のメソッド テスト メッセージ`Id`の削除が存在しません。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドを次に示します。

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

最初に、メソッド実行の配置手順 Act ステップの準備が行われる場所。 シード処理のメッセージを取得し、保持されている`seedMessages`します。 シード処理のメッセージは、データベースに保存されます。 メッセージ、`Id`の`1`削除設定。 ときに、`DeleteMessageAsync`メソッドが実行され、予期されるメッセージには、すべてのメッセージを 1 つを除くが必要、`Id`の`1`します。 `expectedMessages`変数はこの予想される結果を表します。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

メソッドは機能します。`DeleteMessageAsync`を渡すメソッドが実行される、`recId`の`1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

メソッドが最後に、取得した、`Messages`コンテキストからとを比較します、 `expectedMessages` 2 つが等しいことをアサートします。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

比較するために、2 つ`List<Message>`は、同じです。

* によって、メッセージの順序は`Id`します。
* メッセージのペアの比較、`Text`プロパティ。

同様のテスト メソッド、`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`が存在しないメッセージを削除しようとしての結果を確認します。 この場合、データベースで、予期されるメッセージと同じになります、実際のメッセージの後に、`DeleteMessageAsync`メソッドが実行されます。 データベースのコンテンツへの変更はないはず。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>ページ モデルのメソッドの単体テスト

別の一連の単体テストでは、ページ モデルのメソッドのテストを担当します。 メッセージ アプリでは、インデックス ページのモデル内にある、`IndexModel`クラス*src/RazorPagesTestSample/Pages/Index.cshtml.cs*します。

| ページ モデル メソッド | 関数 |
| ----------------- | -------- |
| `OnGetAsync` | UI を使用して、DAL からメッセージを取得、`GetMessagesAsync`メソッド。 |
| `OnPostAddMessageAsync` | 場合、`ModelState`が有効では、呼び出しの`AddMessageAsync`データベースにメッセージを追加します。 |
| `OnPostDeleteAllMessagesAsync` | 呼び出し`DeleteAllMessagesAsync`すべてのデータベース内のメッセージを削除します。 |
| `OnPostDeleteMessageAsync` | 実行`DeleteMessageAsync`でメッセージを削除する、`Id`指定します。 |
| `OnPostAnalyzeMessagesAsync` | 1 つまたは複数のメッセージがデータベース内にある場合は、1 つのメッセージの単語の平均数を計算します。 |

ページ モデルのメソッドがテストで 7 つのテストを使用して、`IndexPageTests`クラス (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)。 テストでは、配置アサート Act の一般的なパターンを使用します。 これらのテストが焦点になりました。

* メソッドのかどうかは、次の正しい動作を決定するときに、`ModelState`が無効です。
* 適切な方法を確認する生成`IActionResult`します。
* プロパティ値の割り当てが正しく行われたことを確認しています。

このテストのグループは、多くの場合、ページ モデルのメソッドが実行される場所、Act の手順の必要なデータを生成するために DAL のメソッドをモックします。 たとえば、`GetMessagesAsync`のメソッド、`AppDbContext`出力を生成するモックを作成します。 ページ モデルのメソッドは、このメソッドを実行するとき、モックは、結果を返します。 データベースからは、データがありません。 これは、ページ モデルのテストで、DAL を使用するための予測可能な信頼性の高いテスト条件を作成します。

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`表示をテストする方法、`GetMessagesAsync`メソッドは、ページ モデルのモックを作成します。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

ときに、`OnGetAsync`メソッドが実行され、Act の手順で、ページ モデルの呼び出し`GetMessagesAsync`メソッド。

単体テストの Act の手順 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` ページ モデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*)。

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` DAL でメソッドがこのメソッドの呼び出しの結果を返しません。 モック バージョンのメソッドは、結果を返します。

`Assert`ステップ: 実際のメッセージ (`actualMessages`) から割り当てられている、`Messages`ページ モデルのプロパティ。 メッセージが割り当てられている場合、型チェックも実行されます。 予測と実際のメッセージの比較には、`Text`プロパティ。 テストのあることをアサート、2 つ`List<Message>`インスタンスは、同じメッセージを含めることができます。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

このグループ内の他のテストは、ページが含まれるモデル オブジェクトを作成、 `DefaultHttpContext`、 `ModelStateDictionary`、`ActionContext`確立するために、 `PageContext`、 `ViewDataDictionary`、および`PageContext`します。 これらは、テストを実施するのに便利です。 たとえば、メッセージ アプリを確立、`ModelState`でエラーが発生`AddModelError`ことを確認する有効な`PageResult`ときに返される`OnPostAddMessageAsync`が実行されます。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>その他の技術情報

* [単体テスト c# dotnet テストと xUnit を使用して .NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [テスト コントローラー](xref:mvc/controllers/testing)
* [コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)
* [統合テスト](xref:test/integration-tests)
* [xUnit.net](https://xunit.github.io/)
* [XUnit.net (.NET Core/ASP.NET Core) の概要](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq のクイック スタート](https://github.com/Moq/moq4/wiki/Quickstart)
