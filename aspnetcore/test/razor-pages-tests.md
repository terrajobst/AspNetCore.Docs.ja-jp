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
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="22366-103">ASP.NET Core で razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="22366-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="22366-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="22366-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="22366-105">ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。</span><span class="sxs-lookup"><span data-stu-id="22366-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="22366-106">データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。</span><span class="sxs-lookup"><span data-stu-id="22366-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="22366-107">Razor ページ アプリのパーツでは、アプリの構築時とは独立してとを単位としてまとめてを動作します。</span><span class="sxs-lookup"><span data-stu-id="22366-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="22366-108">クラスとメソッドには、責任の範囲が制限されます。</span><span class="sxs-lookup"><span data-stu-id="22366-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="22366-109">アプリの動作に関するその他のドキュメントが存在します。</span><span class="sxs-lookup"><span data-stu-id="22366-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="22366-110">コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。</span><span class="sxs-lookup"><span data-stu-id="22366-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="22366-111">このトピックでは、Razor ページ アプリと単体テストの基本的な理解があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="22366-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="22366-112">Razor ページ アプリまたはテストの概念に詳しい場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22366-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* [<span data-ttu-id="22366-113">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="22366-113">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="22366-114">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="22366-114">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="22366-115">単体テスト c# dotnet テストと xUnit を使用して .NET Core</span><span class="sxs-lookup"><span data-stu-id="22366-115">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="22366-116">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="22366-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="22366-117">サンプル プロジェクトは、2 つのアプリで構成されます。</span><span class="sxs-lookup"><span data-stu-id="22366-117">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="22366-118">アプリ</span><span class="sxs-lookup"><span data-stu-id="22366-118">App</span></span>         | <span data-ttu-id="22366-119">プロジェクト フォルダー</span><span class="sxs-lookup"><span data-stu-id="22366-119">Project folder</span></span>                        | <span data-ttu-id="22366-120">説明</span><span class="sxs-lookup"><span data-stu-id="22366-120">Description</span></span> |
| ----------- | ------------------------------------- | ----------- |
| <span data-ttu-id="22366-121">メッセージ アプリ</span><span class="sxs-lookup"><span data-stu-id="22366-121">Message app</span></span> | <span data-ttu-id="22366-122">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="22366-122">*src/RazorPagesTestSample*</span></span>            | <span data-ttu-id="22366-123">により、ユーザーの追加、削除のいずれか、すべてを削除、およびメッセージを分析できます。</span><span class="sxs-lookup"><span data-stu-id="22366-123">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="22366-124">テスト アプリ</span><span class="sxs-lookup"><span data-stu-id="22366-124">Test app</span></span>    | <span data-ttu-id="22366-125">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="22366-125">*tests/RazorPagesTestSample.Tests*</span></span>    | <span data-ttu-id="22366-126">メッセージ アプリの単体テストに使用されます。データ アクセス層 (DAL) とインデックス ページのモデル。</span><span class="sxs-lookup"><span data-stu-id="22366-126">Used to unit test the message app: Data access layer (DAL) and Index page model.</span></span> |

<span data-ttu-id="22366-127">などの IDE、組み込みのテスト機能を使用して、テストを実行できる[Visual Studio](https://visualstudio.microsoft.com)します。</span><span class="sxs-lookup"><span data-stu-id="22366-127">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="22366-128">使用して場合[Visual Studio Code](https://code.visualstudio.com/)またはコマンド プロンプトで次のコマンドを実行するコマンドライン、 *tests/RazorPagesTestSample.Tests*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="22366-128">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="22366-129">メッセージ アプリ組織</span><span class="sxs-lookup"><span data-stu-id="22366-129">Message app organization</span></span>

<span data-ttu-id="22366-130">メッセージ アプリでは、次の特性を持つ単純な Razor ページ メッセージ システムを示します。</span><span class="sxs-lookup"><span data-stu-id="22366-130">The message app is a simple Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="22366-131">アプリのインデックス ページ (*Pages/Index.cshtml*と*Pages/Index.cshtml.cs*) UI とページ モデルのメソッドを追加、削除、およびメッセージ (メッセージあたりの平均の単語) の分析の制御を提供します.</span><span class="sxs-lookup"><span data-stu-id="22366-131">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="22366-132">メッセージは、`Message`クラス (*Data/Message.cs*) 2 つのプロパティを持つ: `Id` (キー) と`Text`(メッセージ)。</span><span class="sxs-lookup"><span data-stu-id="22366-132">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="22366-133">`Text`プロパティは必須であり 200 文字に制限されます。</span><span class="sxs-lookup"><span data-stu-id="22366-133">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="22366-134">使用してメッセージを保存する[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;します。</span><span class="sxs-lookup"><span data-stu-id="22366-134">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="22366-135">アプリのデータベース コンテキスト クラス内でデータ アクセス層 (DAL) に含まれる`AppDbContext`(*Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="22366-135">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="22366-136">DAL メソッドをマーク`virtual`テストで使用されるメソッドのモックを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="22366-136">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="22366-137">データベースがアプリの起動時に空の場合、メッセージ ストアは、3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="22366-137">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="22366-138">これら*シード処理されるメッセージ*テストでも使用されます。</span><span class="sxs-lookup"><span data-stu-id="22366-138">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="22366-139">&#8224;EF トピック[InMemory のテスト](/ef/core/miscellaneous/testing/in-memory)MSTest を使用したテストにメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="22366-139">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="22366-140">このトピックでは、 [xUnit](https://xunit.github.io/)テスト フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="22366-140">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="22366-141">テストの概念と別のテスト フレームワーク間でのテストの実装は似ていますが、同一ではないです。</span><span class="sxs-lookup"><span data-stu-id="22366-141">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="22366-142">アプリは、リポジトリ パターンを使用しないしの有効な例はありませんが、 [Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)、Razor ページは、これらのパターンの開発をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="22366-142">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="22366-143">詳細については、次を参照してください。[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テスト コント ローラー ロジック](/aspnet/core/mvc/controllers/testing)(このサンプルは、リポジトリ パターンを実装)。</span><span class="sxs-lookup"><span data-stu-id="22366-143">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="22366-144">組織のアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="22366-144">Test app organization</span></span>

<span data-ttu-id="22366-145">テスト アプリが内部でコンソール アプリ、 *tests/RazorPagesTestSample.Tests*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="22366-145">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="22366-146">アプリ フォルダーのテスト</span><span class="sxs-lookup"><span data-stu-id="22366-146">Test app folder</span></span> | <span data-ttu-id="22366-147">説明</span><span class="sxs-lookup"><span data-stu-id="22366-147">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="22366-148">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="22366-148">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="22366-149">*DataAccessLayerTest.cs* DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="22366-149">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="22366-150">*IndexPageTests.cs*インデックス ページのモデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="22366-150">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="22366-151">*ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="22366-151">*Utilities*</span></span>     | <span data-ttu-id="22366-152">含まれています、`TestDbContextOptions`メソッドを各テストのベースラインの状態にデータベースをリセットするために、各 DAL の単体テストのコンテキストのオプションを新しいデータベースに作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="22366-152">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="22366-153">テスト フレームワークは[xUnit](https://xunit.github.io/)します。</span><span class="sxs-lookup"><span data-stu-id="22366-153">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="22366-154">オブジェクトのモック作成フレームワークが[Moq](https://github.com/moq/moq4)します。</span><span class="sxs-lookup"><span data-stu-id="22366-154">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="22366-155">単体テストのデータ アクセス層 (DAL)</span><span class="sxs-lookup"><span data-stu-id="22366-155">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="22366-156">メッセージ アプリに含まれている 4 つの方法で、DAL が、`AppDbContext`クラス (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="22366-156">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="22366-157">各メソッドでは、テスト アプリで 1 つまたは 2 つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="22366-157">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="22366-158">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="22366-158">DAL method</span></span>               | <span data-ttu-id="22366-159">関数</span><span class="sxs-lookup"><span data-stu-id="22366-159">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="22366-160">取得、`List<Message>`データベースを順に並べ替えてから、`Text`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="22366-160">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="22366-161">追加、`Message`データベースにします。</span><span class="sxs-lookup"><span data-stu-id="22366-161">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="22366-162">すべてを削除します`Message`データベースからのエントリ。</span><span class="sxs-lookup"><span data-stu-id="22366-162">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="22366-163">1 つを削除します。`Message`によってデータベースから`Id`します。</span><span class="sxs-lookup"><span data-stu-id="22366-163">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="22366-164">DAL の単体テストを必要と[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)新しいを作成するときに`AppDbContext`テストごとにします。</span><span class="sxs-lookup"><span data-stu-id="22366-164">Unit tests of the DAL require [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="22366-165">作成する方法、`DbContextOptions`は、各テストを使用する、 [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):</span><span class="sxs-lookup"><span data-stu-id="22366-165">One approach to creating the `DbContextOptions` for each test is to use a [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="22366-166">このアプローチの問題は、各テストがどのような状態、前のテストが左でデータベースを受信します。</span><span class="sxs-lookup"><span data-stu-id="22366-166">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="22366-167">互いに干渉しないアトミック単位テストを作成しようとするとき、これは問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="22366-167">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="22366-168">強制的に、`AppDbContext`テストごとに新しいデータベースのコンテキストを使用するを指定する、`DbContextOptions`新しいサービス プロバイダーに基づいているインスタンス。</span><span class="sxs-lookup"><span data-stu-id="22366-168">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="22366-169">テスト アプリを使用してこれを行う方法を示しています。 その`Utilities`クラス メソッド`TestDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*)。</span><span class="sxs-lookup"><span data-stu-id="22366-169">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="22366-170">使用して、 `DbContextOptions` DAL 単体テストによって、新しいデータベース インスタンスにアトミックに実行するには、各テスト。</span><span class="sxs-lookup"><span data-stu-id="22366-170">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="22366-171">内の各テスト メソッド、`DataAccessLayerTest`クラス (*UnitTests/DataAccessLayerTest.cs*) 配置 Act アサートと同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="22366-171">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="22366-172">配置します。データベースがテストの構成や、予想される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="22366-172">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="22366-173">Act:テストを実行します。</span><span class="sxs-lookup"><span data-stu-id="22366-173">Act: The test is executed.</span></span>
1. <span data-ttu-id="22366-174">アサートします。アサーションは、テスト結果が成功したかどうかを決定されます。</span><span class="sxs-lookup"><span data-stu-id="22366-174">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="22366-175">たとえば、`DeleteMessageAsync`で識別される 1 つのメッセージを削除するため、メソッドはその`Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="22366-175">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="22366-176">このメソッドの 2 つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="22366-176">There are two tests for this method.</span></span> <span data-ttu-id="22366-177">メッセージがデータベースに存在する場合、メソッドはメッセージを削除します。 1 つのテストを確認します。</span><span class="sxs-lookup"><span data-stu-id="22366-177">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="22366-178">データベースを変更しないこと、その他のメソッド テスト メッセージ`Id`の削除が存在しません。</span><span class="sxs-lookup"><span data-stu-id="22366-178">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="22366-179">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="22366-179">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="22366-180">最初に、メソッド実行の配置手順 Act ステップの準備が行われる場所。</span><span class="sxs-lookup"><span data-stu-id="22366-180">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="22366-181">シード処理のメッセージを取得し、保持されている`seedMessages`します。</span><span class="sxs-lookup"><span data-stu-id="22366-181">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="22366-182">シード処理のメッセージは、データベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="22366-182">The seeding messages are saved into the database.</span></span> <span data-ttu-id="22366-183">メッセージ、`Id`の`1`削除設定。</span><span class="sxs-lookup"><span data-stu-id="22366-183">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="22366-184">ときに、`DeleteMessageAsync`メソッドが実行され、予期されるメッセージには、すべてのメッセージを 1 つを除くが必要、`Id`の`1`します。</span><span class="sxs-lookup"><span data-stu-id="22366-184">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="22366-185">`expectedMessages`変数はこの予想される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="22366-185">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="22366-186">メソッドは機能します。`DeleteMessageAsync`を渡すメソッドが実行される、`recId`の`1`:</span><span class="sxs-lookup"><span data-stu-id="22366-186">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="22366-187">メソッドが最後に、取得した、`Messages`コンテキストからとを比較します、 `expectedMessages` 2 つが等しいことをアサートします。</span><span class="sxs-lookup"><span data-stu-id="22366-187">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="22366-188">比較するために、2 つ`List<Message>`は、同じです。</span><span class="sxs-lookup"><span data-stu-id="22366-188">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="22366-189">によって、メッセージの順序は`Id`します。</span><span class="sxs-lookup"><span data-stu-id="22366-189">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="22366-190">メッセージのペアの比較、`Text`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="22366-190">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="22366-191">同様のテスト メソッド、`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`が存在しないメッセージを削除しようとしての結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="22366-191">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="22366-192">この場合、データベースで、予期されるメッセージと同じになります、実際のメッセージの後に、`DeleteMessageAsync`メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="22366-192">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="22366-193">データベースのコンテンツへの変更はないはず。</span><span class="sxs-lookup"><span data-stu-id="22366-193">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="22366-194">ページ モデルのメソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="22366-194">Unit tests of the page model methods</span></span>

<span data-ttu-id="22366-195">別の一連の単体テストでは、ページ モデルのメソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="22366-195">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="22366-196">メッセージ アプリでは、インデックス ページのモデル内にある、`IndexModel`クラス*src/RazorPagesTestSample/Pages/Index.cshtml.cs*します。</span><span class="sxs-lookup"><span data-stu-id="22366-196">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="22366-197">ページ モデル メソッド</span><span class="sxs-lookup"><span data-stu-id="22366-197">Page model method</span></span> | <span data-ttu-id="22366-198">関数</span><span class="sxs-lookup"><span data-stu-id="22366-198">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="22366-199">UI を使用して、DAL からメッセージを取得、`GetMessagesAsync`メソッド。</span><span class="sxs-lookup"><span data-stu-id="22366-199">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="22366-200">場合、`ModelState`が有効では、呼び出しの`AddMessageAsync`データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="22366-200">If the `ModelState` is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="22366-201">呼び出し`DeleteAllMessagesAsync`すべてのデータベース内のメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="22366-201">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="22366-202">実行`DeleteMessageAsync`でメッセージを削除する、`Id`指定します。</span><span class="sxs-lookup"><span data-stu-id="22366-202">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="22366-203">1 つまたは複数のメッセージがデータベース内にある場合は、1 つのメッセージの単語の平均数を計算します。</span><span class="sxs-lookup"><span data-stu-id="22366-203">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="22366-204">ページ モデルのメソッドがテストで 7 つのテストを使用して、`IndexPageTests`クラス (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)。</span><span class="sxs-lookup"><span data-stu-id="22366-204">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="22366-205">テストでは、配置アサート Act の一般的なパターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="22366-205">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="22366-206">これらのテストが焦点になりました。</span><span class="sxs-lookup"><span data-stu-id="22366-206">These tests focus on:</span></span>

* <span data-ttu-id="22366-207">メソッドのかどうかは、次の正しい動作を決定するときに、`ModelState`が無効です。</span><span class="sxs-lookup"><span data-stu-id="22366-207">Determining if the methods follow the correct behavior when the `ModelState` is invalid.</span></span>
* <span data-ttu-id="22366-208">適切な方法を確認する生成`IActionResult`します。</span><span class="sxs-lookup"><span data-stu-id="22366-208">Confirming the methods produce the correct `IActionResult`.</span></span>
* <span data-ttu-id="22366-209">プロパティ値の割り当てが正しく行われたことを確認しています。</span><span class="sxs-lookup"><span data-stu-id="22366-209">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="22366-210">このテストのグループは、多くの場合、ページ モデルのメソッドが実行される場所、Act の手順の必要なデータを生成するために DAL のメソッドをモックします。</span><span class="sxs-lookup"><span data-stu-id="22366-210">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="22366-211">たとえば、`GetMessagesAsync`のメソッド、`AppDbContext`出力を生成するモックを作成します。</span><span class="sxs-lookup"><span data-stu-id="22366-211">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="22366-212">ページ モデルのメソッドは、このメソッドを実行するとき、モックは、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="22366-212">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="22366-213">データベースからは、データがありません。</span><span class="sxs-lookup"><span data-stu-id="22366-213">The data doesn't come from the database.</span></span> <span data-ttu-id="22366-214">これは、ページ モデルのテストで、DAL を使用するための予測可能な信頼性の高いテスト条件を作成します。</span><span class="sxs-lookup"><span data-stu-id="22366-214">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="22366-215">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`表示をテストする方法、`GetMessagesAsync`メソッドは、ページ モデルのモックを作成します。</span><span class="sxs-lookup"><span data-stu-id="22366-215">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="22366-216">ときに、`OnGetAsync`メソッドが実行され、Act の手順で、ページ モデルの呼び出し`GetMessagesAsync`メソッド。</span><span class="sxs-lookup"><span data-stu-id="22366-216">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="22366-217">単体テストの Act の手順 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)。</span><span class="sxs-lookup"><span data-stu-id="22366-217">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="22366-218">`IndexPage` ページ モデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="22366-218">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="22366-219">`GetMessagesAsync` DAL でメソッドがこのメソッドの呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="22366-219">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="22366-220">モック バージョンのメソッドは、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="22366-220">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="22366-221">`Assert`ステップ: 実際のメッセージ (`actualMessages`) から割り当てられている、`Messages`ページ モデルのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="22366-221">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="22366-222">メッセージが割り当てられている場合、型チェックも実行されます。</span><span class="sxs-lookup"><span data-stu-id="22366-222">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="22366-223">予測と実際のメッセージの比較には、`Text`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="22366-223">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="22366-224">テストのあることをアサート、2 つ`List<Message>`インスタンスは、同じメッセージを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="22366-224">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="22366-225">このグループ内の他のテストは、ページが含まれるモデル オブジェクトを作成、 `DefaultHttpContext`、 `ModelStateDictionary`、`ActionContext`確立するために、 `PageContext`、 `ViewDataDictionary`、および`PageContext`します。</span><span class="sxs-lookup"><span data-stu-id="22366-225">Other tests in this group create page model objects that include the `DefaultHttpContext`, the `ModelStateDictionary`, an `ActionContext` to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="22366-226">これらは、テストを実施するのに便利です。</span><span class="sxs-lookup"><span data-stu-id="22366-226">These are useful in conducting tests.</span></span> <span data-ttu-id="22366-227">たとえば、メッセージ アプリを確立、`ModelState`でエラーが発生`AddModelError`ことを確認する有効な`PageResult`ときに返される`OnPostAddMessageAsync`が実行されます。</span><span class="sxs-lookup"><span data-stu-id="22366-227">For example, the message app establishes a `ModelState` error with `AddModelError` to check that a valid `PageResult` is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="22366-228">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="22366-228">Additional resources</span></span>

* [<span data-ttu-id="22366-229">単体テスト c# dotnet テストと xUnit を使用して .NET Core</span><span class="sxs-lookup"><span data-stu-id="22366-229">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="22366-230">テスト コントローラー</span><span class="sxs-lookup"><span data-stu-id="22366-230">Test controllers</span></span>](xref:mvc/controllers/testing)
* <span data-ttu-id="22366-231">[コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="22366-231">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* [<span data-ttu-id="22366-232">統合テスト</span><span class="sxs-lookup"><span data-stu-id="22366-232">Integration tests</span></span>](xref:test/integration-tests)
* [<span data-ttu-id="22366-233">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="22366-233">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="22366-234">XUnit.net (.NET Core/ASP.NET Core) の概要</span><span class="sxs-lookup"><span data-stu-id="22366-234">Getting started with xUnit.net (.NET Core/ASP.NET Core)</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="22366-235">Moq</span><span class="sxs-lookup"><span data-stu-id="22366-235">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="22366-236">Moq のクイック スタート</span><span class="sxs-lookup"><span data-stu-id="22366-236">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)
