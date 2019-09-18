---
title: ASP.NET Core での単体テストの Razor Pages
author: guardrex
description: Razor Pages アプリの単体テストを作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: afac97d686ef190ebb92d20a55a15dd774b0d1de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081423"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core での単体テストの Razor Pages

作成者: [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。 データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。

* Razor Pages アプリの一部は、アプリの構築時に個別に1つの単位として機能します。
* クラスとメソッドには、限られた範囲の責任があります。
* アプリの動作に関する追加のドキュメントがあります。
* コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。

このトピックでは、Razor Pages アプリと単体テストについての基本的な知識があることを前提としています。 Razor Pages アプリまたはテストの概念に慣れていない場合は、次のトピックを参照してください。

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Dotnet テストC#と xunit を使用した .net Core での単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプルプロジェクトは、次の2つのアプリで構成されています。

| アプリ         | プロジェクトフォルダー                     | 説明 |
| ----------- | ---------------------------------- | ----------- |
| メッセージアプリ | *src/RazorPagesTestSample*         | ユーザーがメッセージの追加、1つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析を実行できるようにします (メッセージあたりの平均単語数を検索します)。 |
| アプリのテスト    | *テスト/RazorPagesTestSample* | メッセージアプリの DAL および Index ページモデルの単体テストに使用されます。 |

テストは、 [Visual Studio](/visualstudio/test/unit-test-your-code)や[VISUAL STUDIO FOR MAC](/dotnet/core/tutorials/using-on-mac-vs-full-solution)など、IDE の組み込みのテスト機能を使用して実行できます。 [Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、Test */RazorPagesTestSample*フォルダーでコマンドプロンプトから次のコマンドを実行します。

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>メッセージアプリの組織

メッセージアプリは、次の特性を持つ Razor Pages メッセージシステムです。

* アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージあたりの平均単語数を検索します)。
* メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。 `Text`プロパティは必須であり、200文字までに制限されています。
* メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。
* アプリには、データベースコンテキストクラス`AppDbContext` (*Data/appdbcontext .cs*) に DAL が含まれています。 DAL メソッドはとマーク`virtual`されます。これにより、テストで使用するメソッドをモックできます。
* アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。 これらのシード処理された*メッセージ*は、テストでも使用されます。

&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。 このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。 テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。

サンプルアプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。 詳細については、「[インフラストラクチャの永続化層の設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と<xref:mvc/controllers/testing> 「」を参照してください。

## <a name="test-app-organization"></a>テストアプリの組織

テストアプリは、 *tests/RazorPagesTestSample*フォルダー内のコンソールアプリです。

| テストアプリフォルダー | 説明 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs*には、DAL の単体テストが含まれています。</li><li>*IndexPageTests.cs*には、インデックスページモデルの単体テストが含まれています。</li></ul> |
| *ユーティリティ*     | 各 DAL 単体テストの新しいデータベースコンテキストオプションを作成するために使用するメソッドが含まれています。これにより、各テストのベースライン条件にデータベースがリセットされます。`TestDbContextOptions` |

テストフレームワークは[Xunit](https://xunit.github.io/)です。 オブジェクトモックフレームワークは[Moq](https://github.com/moq/moq4)です。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>データアクセス層 (DAL) の単体テスト

メッセージアプリには、 `AppDbContext`クラス (*src/RazorPagesTestSample/Data/appdbcontext .cs*) に含まれる4つのメソッドを持つ DAL があります。 各メソッドには、テストアプリに1つまたは2つの単体テストがあります。

| DAL メソッド               | 関数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | プロパティによって並べ替えられたデータベースからを`List<Message>`取得します。 `Text` |
| `AddMessageAsync`        | を`Message`データベースに追加します。                                          |
| `DeleteAllMessagesAsync` | データベースから`Message`すべてのエントリを削除します。                           |
| `DeleteMessageAsync`     | によって`Message` `Id`、1つのをデータベースから削除します。                      |

DAL の単体テストでは<xref:Microsoft.EntityFrameworkCore.DbContextOptions> 、各テストに`AppDbContext`新しいを作成する必要があります。 各テスト`DbContextOptions`のを作成する方法の1つは、 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>を使用することです。

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

この方法の問題は、各テストが、前のテストでどのような状態でデータベースを受け取るかということです。 相互に干渉しない atomic 単体テストを作成しようとすると、問題が発生する可能性があります。 が`AppDbContext`各テストに新しいデータベースコンテキストを使用するように強制するに`DbContextOptions`は、新しいサービスプロバイダーに基づくインスタンスを指定します。 テストアプリでは、 `Utilities`クラスメソッド`TestDbContextOptions`を使用してこれを行う方法を示しています (tests/*RazorPagesTestSample/utilities/* utilities)。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL 単体`DbContextOptions`テストでを使用すると、新しいデータベースインスタンスを使用して、各テストをアトミックに実行できます。

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest`クラス (*unittests/dataaccessレイヤーテスト .cs*) の各テストメソッドは、次のように同様の配置/操作アサートパターンに従います。

1. 名簿データベースがテスト用に構成されているか、予期される結果が定義されています。
1. 作業テストが実行されます。
1. アサートアサーションは、テスト結果が成功であるかどうかを判断するために作成されます。

たとえば、 `DeleteMessageAsync`メソッドは、 `Id` (*src/RazorPagesTestSample/Data/appdbcontext .cs*) によって識別される1つのメッセージを削除します。

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

このメソッドには2つのテストがあります。 1つのテストでは、メッセージがデータベース内に存在する場合に、メソッドによってメッセージが削除されることを確認します。 もう1つの方法では、削除するメッセージ`Id`が存在しない場合にデータベースが変更されないことをテストします。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドは次のようになります。

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

まず、メソッドは、Act ステップの準備が行われる配置手順を実行します。 シード処理メッセージは、で`seedMessages`取得および保持されます。 シード処理メッセージはデータベースに保存されます。 `Id` の`1`を持つメッセージは、削除の対象として設定されます。 メソッドが実行されるとき、予期されるメッセージには、 `Id`のがの`1`メッセージを除くすべてのメッセージが含まれている必要があります。 `DeleteMessageAsync` 変数`expectedMessages`は、この予想される結果を表します。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

メソッドは次のように動作します。メソッドは、の`recId` `1`を渡して実行されます。 `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最後に、メソッドはコンテキスト`Messages`からを取得し、2つの`expectedMessages`が等しいことをアサートと比較します。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

2つ`List<Message>`が同じであることを比較するために、次のようにします。

* メッセージはによって`Id`並べ替えられます。
* メッセージのペアは、 `Text`プロパティで比較されます。

同様のテストメソッドは`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 、存在しないメッセージを削除しようとした結果を確認します。 この場合、データベース内の予期されるメッセージは、 `DeleteMessageAsync`メソッドが実行された後の実際のメッセージと同じである必要があります。 データベースのコンテンツを変更することはできません。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>ページモデルメソッドの単体テスト

別の単体テストのセットは、ページモデルメソッドのテストを担当します。 メッセージアプリでは、インデックスページモデルは、 `IndexModel` *src/RazorPagesTestSample/Pages/index. cshtml. .cs*のクラスにあります。

| ページモデルメソッド | 関数 |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync`メソッドを使用して、UI の DAL からメッセージを取得します。 |
| `OnPostAddMessageAsync` | [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が有効な場合、は`AddMessageAsync`を呼び出して、データベースにメッセージを追加します。 |
| `OnPostDeleteAllMessagesAsync` | を`DeleteAllMessagesAsync`呼び出して、データベース内のすべてのメッセージを削除します。 |
| `OnPostDeleteMessageAsync` | を`DeleteMessageAsync`実行して、 `Id`指定したを持つメッセージを削除します。 |
| `OnPostAnalyzeMessagesAsync` | 1つ以上のメッセージがデータベースに含まれている場合は、によって、メッセージあたりの平均単語数が計算されます。 |

ページモデルメソッドは、 `IndexPageTests`クラスで7つのテストを使用してテストされます (*テスト/RazorPagesTestSample/unittests/indexpagetests .cs*)。 テストでは、使い慣れた配置/アサートパターンが使用されます。 これらのテストは次のことに焦点を当てます。

* [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。
* メソッドによって正しい<xref:Microsoft.AspNetCore.Mvc.IActionResult>が生成されることを確認しています。
* プロパティ値の割り当てが正しく行われていることを確認しています。

この一連のテストでは、多くの場合、ページモデルメソッドを実行する Act ステップに対して予期されるデータを生成するために DAL のメソッドがモックされます。 たとえば`GetMessagesAsync` 、`AppDbContext`のメソッドは、出力を生成するモックです。 ページモデルメソッドがこのメソッドを実行すると、モックは結果を返します。 データはデータベースから取得されません。 これにより、ページモデルテストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。

テスト`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`では、ページ`GetMessagesAsync`モデルに対してメソッドがモックされる方法を示しています。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Act ステップで`GetMessagesAsync` メソッドを実行すると、ページモデルのメソッドが`OnGetAsync`呼び出されます。

単体テストの Act ステップ (*tests/RazorPagesTestSample/unittests/IndexPageTests .cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`ページモデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index. cshtml. .cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync`のメソッドは、このメソッド呼び出しの結果を返しません。 メソッドのモックバージョンは、結果を返します。

この手順では、ページモデルの`actualMessages` `Messages`プロパティから実際のメッセージ () が割り当てられます。 `Assert` 型チェックは、メッセージが割り当てられたときにも実行されます。 予想されるメッセージと実際のメッセージは`Text` 、プロパティによって比較されます。 テストでは、2つ`List<Message>`のインスタンスに同じメッセージが含まれていることをアサートします。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

このグループの他のテストでは、、、および<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>を確立<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `PageContext`するためのを含むページモデルオブジェクトが作成されます。 これらは、テストの実施に役立ちます。 たとえば、メッセージアプリは、の実行`ModelState`時`OnPostAddMessageAsync`に<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>有効<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>なが返されることを確認するために、でエラーを設定します。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>その他の技術情報

* [Dotnet テストC#と xunit を使用した .net Core での単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net の概要:.Net Core と .NET SDK コマンドラインの使用](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq クイックスタート](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。 データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。

* Razor Pages アプリの一部は、アプリの構築時に個別に1つの単位として機能します。
* クラスとメソッドには、限られた範囲の責任があります。
* アプリの動作に関する追加のドキュメントがあります。
* コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。

このトピックでは、Razor Pages アプリと単体テストについての基本的な知識があることを前提としています。 Razor Pages アプリまたはテストの概念に慣れていない場合は、次のトピックを参照してください。

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Dotnet テストC#と xunit を使用した .net Core での単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプルプロジェクトは、次の2つのアプリで構成されています。

| アプリ         | プロジェクトフォルダー                     | 説明 |
| ----------- | ---------------------------------- | ----------- |
| メッセージアプリ | *src/RazorPagesTestSample*         | ユーザーがメッセージの追加、1つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析を実行できるようにします (メッセージあたりの平均単語数を検索します)。 |
| アプリのテスト    | *テスト/RazorPagesTestSample* | メッセージアプリの DAL および Index ページモデルの単体テストに使用されます。 |

テストは、 [Visual Studio](/visualstudio/test/unit-test-your-code)や[VISUAL STUDIO FOR MAC](/dotnet/core/tutorials/using-on-mac-vs-full-solution)など、IDE の組み込みのテスト機能を使用して実行できます。 [Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、Test */RazorPagesTestSample*フォルダーでコマンドプロンプトから次のコマンドを実行します。

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>メッセージアプリの組織

メッセージアプリは、次の特性を持つ Razor Pages メッセージシステムです。

* アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージあたりの平均単語数を検索します)。
* メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。 `Text`プロパティは必須であり、200文字までに制限されています。
* メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。
* アプリには、データベースコンテキストクラス`AppDbContext` (*Data/appdbcontext .cs*) に DAL が含まれています。 DAL メソッドはとマーク`virtual`されます。これにより、テストで使用するメソッドをモックできます。
* アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。 これらのシード処理された*メッセージ*は、テストでも使用されます。

&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。 このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。 テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。

サンプルアプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。 詳細については、「[インフラストラクチャの永続化層の設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と<xref:mvc/controllers/testing> 「」を参照してください。

## <a name="test-app-organization"></a>テストアプリの組織

テストアプリは、 *tests/RazorPagesTestSample*フォルダー内のコンソールアプリです。

| テストアプリフォルダー | 説明 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs*には、DAL の単体テストが含まれています。</li><li>*IndexPageTests.cs*には、インデックスページモデルの単体テストが含まれています。</li></ul> |
| *ユーティリティ*     | 各 DAL 単体テストの新しいデータベースコンテキストオプションを作成するために使用するメソッドが含まれています。これにより、各テストのベースライン条件にデータベースがリセットされます。`TestDbContextOptions` |

テストフレームワークは[Xunit](https://xunit.github.io/)です。 オブジェクトモックフレームワークは[Moq](https://github.com/moq/moq4)です。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>データアクセス層 (DAL) の単体テスト

メッセージアプリには、 `AppDbContext`クラス (*src/RazorPagesTestSample/Data/appdbcontext .cs*) に含まれる4つのメソッドを持つ DAL があります。 各メソッドには、テストアプリに1つまたは2つの単体テストがあります。

| DAL メソッド               | 関数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | プロパティによって並べ替えられたデータベースからを`List<Message>`取得します。 `Text` |
| `AddMessageAsync`        | を`Message`データベースに追加します。                                          |
| `DeleteAllMessagesAsync` | データベースから`Message`すべてのエントリを削除します。                           |
| `DeleteMessageAsync`     | によって`Message` `Id`、1つのをデータベースから削除します。                      |

DAL の単体テストでは<xref:Microsoft.EntityFrameworkCore.DbContextOptions> 、各テストに`AppDbContext`新しいを作成する必要があります。 各テスト`DbContextOptions`のを作成する方法の1つは、 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>を使用することです。

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

この方法の問題は、各テストが、前のテストでどのような状態でデータベースを受け取るかということです。 相互に干渉しない atomic 単体テストを作成しようとすると、問題が発生する可能性があります。 が`AppDbContext`各テストに新しいデータベースコンテキストを使用するように強制するに`DbContextOptions`は、新しいサービスプロバイダーに基づくインスタンスを指定します。 テストアプリでは、 `Utilities`クラスメソッド`TestDbContextOptions`を使用してこれを行う方法を示しています (tests/*RazorPagesTestSample/utilities/* utilities)。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL 単体`DbContextOptions`テストでを使用すると、新しいデータベースインスタンスを使用して、各テストをアトミックに実行できます。

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest`クラス (*unittests/dataaccessレイヤーテスト .cs*) の各テストメソッドは、次のように同様の配置/操作アサートパターンに従います。

1. 名簿データベースがテスト用に構成されているか、予期される結果が定義されています。
1. 作業テストが実行されます。
1. アサートアサーションは、テスト結果が成功であるかどうかを判断するために作成されます。

たとえば、 `DeleteMessageAsync`メソッドは、 `Id` (*src/RazorPagesTestSample/Data/appdbcontext .cs*) によって識別される1つのメッセージを削除します。

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

このメソッドには2つのテストがあります。 1つのテストでは、メッセージがデータベース内に存在する場合に、メソッドによってメッセージが削除されることを確認します。 もう1つの方法では、削除するメッセージ`Id`が存在しない場合にデータベースが変更されないことをテストします。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドは次のようになります。

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

まず、メソッドは、Act ステップの準備が行われる配置手順を実行します。 シード処理メッセージは、で`seedMessages`取得および保持されます。 シード処理メッセージはデータベースに保存されます。 `Id` の`1`を持つメッセージは、削除の対象として設定されます。 メソッドが実行されるとき、予期されるメッセージには、 `Id`のがの`1`メッセージを除くすべてのメッセージが含まれている必要があります。 `DeleteMessageAsync` 変数`expectedMessages`は、この予想される結果を表します。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

メソッドは次のように動作します。メソッドは、の`recId` `1`を渡して実行されます。 `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最後に、メソッドはコンテキスト`Messages`からを取得し、2つの`expectedMessages`が等しいことをアサートと比較します。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

2つ`List<Message>`が同じであることを比較するために、次のようにします。

* メッセージはによって`Id`並べ替えられます。
* メッセージのペアは、 `Text`プロパティで比較されます。

同様のテストメソッドは`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 、存在しないメッセージを削除しようとした結果を確認します。 この場合、データベース内の予期されるメッセージは、 `DeleteMessageAsync`メソッドが実行された後の実際のメッセージと同じである必要があります。 データベースのコンテンツを変更することはできません。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>ページモデルメソッドの単体テスト

別の単体テストのセットは、ページモデルメソッドのテストを担当します。 メッセージアプリでは、インデックスページモデルは、 `IndexModel` *src/RazorPagesTestSample/Pages/index. cshtml. .cs*のクラスにあります。

| ページモデルメソッド | 関数 |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync`メソッドを使用して、UI の DAL からメッセージを取得します。 |
| `OnPostAddMessageAsync` | [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が有効な場合、は`AddMessageAsync`を呼び出して、データベースにメッセージを追加します。 |
| `OnPostDeleteAllMessagesAsync` | を`DeleteAllMessagesAsync`呼び出して、データベース内のすべてのメッセージを削除します。 |
| `OnPostDeleteMessageAsync` | を`DeleteMessageAsync`実行して、 `Id`指定したを持つメッセージを削除します。 |
| `OnPostAnalyzeMessagesAsync` | 1つ以上のメッセージがデータベースに含まれている場合は、によって、メッセージあたりの平均単語数が計算されます。 |

ページモデルメソッドは、 `IndexPageTests`クラスで7つのテストを使用してテストされます (*テスト/RazorPagesTestSample/unittests/indexpagetests .cs*)。 テストでは、使い慣れた配置/アサートパターンが使用されます。 これらのテストは次のことに焦点を当てます。

* [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。
* メソッドによって正しい<xref:Microsoft.AspNetCore.Mvc.IActionResult>が生成されることを確認しています。
* プロパティ値の割り当てが正しく行われていることを確認しています。

この一連のテストでは、多くの場合、ページモデルメソッドを実行する Act ステップに対して予期されるデータを生成するために DAL のメソッドがモックされます。 たとえば`GetMessagesAsync` 、`AppDbContext`のメソッドは、出力を生成するモックです。 ページモデルメソッドがこのメソッドを実行すると、モックは結果を返します。 データはデータベースから取得されません。 これにより、ページモデルテストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。

テスト`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`では、ページ`GetMessagesAsync`モデルに対してメソッドがモックされる方法を示しています。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Act ステップで`GetMessagesAsync` メソッドを実行すると、ページモデルのメソッドが`OnGetAsync`呼び出されます。

単体テストの Act ステップ (*tests/RazorPagesTestSample/unittests/IndexPageTests .cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`ページモデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index. cshtml. .cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync`のメソッドは、このメソッド呼び出しの結果を返しません。 メソッドのモックバージョンは、結果を返します。

この手順では、ページモデルの`actualMessages` `Messages`プロパティから実際のメッセージ () が割り当てられます。 `Assert` 型チェックは、メッセージが割り当てられたときにも実行されます。 予想されるメッセージと実際のメッセージは`Text` 、プロパティによって比較されます。 テストでは、2つ`List<Message>`のインスタンスに同じメッセージが含まれていることをアサートします。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

このグループの他のテストでは、、、および<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>を確立<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `PageContext`するためのを含むページモデルオブジェクトが作成されます。 これらは、テストの実施に役立ちます。 たとえば、メッセージアプリは、の実行`ModelState`時`OnPostAddMessageAsync`に<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>有効<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>なが返されることを確認するために、でエラーを設定します。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>その他の技術情報

* [Dotnet テストC#と xunit を使用した .net Core での単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net の概要:.Net Core と .NET SDK コマンドラインの使用](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq クイックスタート](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
