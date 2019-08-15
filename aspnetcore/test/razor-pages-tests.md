---
title: ASP.NET Core での単体テストの Razor Pages
author: guardrex
description: Razor Pages アプリの単体テストを作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 35feb5dd95fa79ceca7ff03523cef30d29ccbdd3
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022570"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="10380-103">ASP.NET Core での単体テストの Razor Pages</span><span class="sxs-lookup"><span data-stu-id="10380-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="10380-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10380-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10380-105">ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。</span><span class="sxs-lookup"><span data-stu-id="10380-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="10380-106">データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。</span><span class="sxs-lookup"><span data-stu-id="10380-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="10380-107">Razor Pages アプリの一部は、アプリの構築時に個別に1つの単位として機能します。</span><span class="sxs-lookup"><span data-stu-id="10380-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="10380-108">クラスとメソッドには、限られた範囲の責任があります。</span><span class="sxs-lookup"><span data-stu-id="10380-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="10380-109">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="10380-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="10380-110">コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。</span><span class="sxs-lookup"><span data-stu-id="10380-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="10380-111">このトピックでは、Razor Pages アプリと単体テストについての基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="10380-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="10380-112">Razor Pages アプリまたはテストの概念に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="10380-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="10380-113">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-113">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="10380-114">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="10380-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="10380-115">サンプルプロジェクトは、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="10380-115">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="10380-116">アプリ</span><span class="sxs-lookup"><span data-stu-id="10380-116">App</span></span>         | <span data-ttu-id="10380-117">プロジェクトフォルダー</span><span class="sxs-lookup"><span data-stu-id="10380-117">Project folder</span></span>                     | <span data-ttu-id="10380-118">説明</span><span class="sxs-lookup"><span data-stu-id="10380-118">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="10380-119">メッセージアプリ</span><span class="sxs-lookup"><span data-stu-id="10380-119">Message app</span></span> | <span data-ttu-id="10380-120">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="10380-120">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="10380-121">ユーザーがメッセージの追加、1つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析を実行できるようにします (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="10380-121">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="10380-122">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="10380-122">Test app</span></span>    | <span data-ttu-id="10380-123">*テスト/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="10380-123">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="10380-124">メッセージアプリの DAL および Index ページモデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="10380-124">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="10380-125">テストは、 [Visual Studio](/visualstudio/test/unit-test-your-code)や[VISUAL STUDIO FOR MAC](/dotnet/core/tutorials/using-on-mac-vs-full-solution)など、IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="10380-125">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="10380-126">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、Test */RazorPagesTestSample*フォルダーでコマンドプロンプトから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="10380-126">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="10380-127">メッセージアプリの組織</span><span class="sxs-lookup"><span data-stu-id="10380-127">Message app organization</span></span>

<span data-ttu-id="10380-128">メッセージアプリは、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="10380-128">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="10380-129">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="10380-129">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="10380-130">メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="10380-130">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="10380-131">`Text`プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="10380-131">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="10380-132">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="10380-132">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="10380-133">アプリには、データベースコンテキストクラス`AppDbContext` (*Data/appdbcontext .cs*) に DAL が含まれています。</span><span class="sxs-lookup"><span data-stu-id="10380-133">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="10380-134">DAL メソッドはとマーク`virtual`されます。これにより、テストで使用するメソッドをモックできます。</span><span class="sxs-lookup"><span data-stu-id="10380-134">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="10380-135">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="10380-135">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="10380-136">これらのシード処理された*メッセージ*は、テストでも使用されます。</span><span class="sxs-lookup"><span data-stu-id="10380-136">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="10380-137">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="10380-137">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="10380-138">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="10380-138">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="10380-139">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="10380-139">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="10380-140">サンプルアプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="10380-140">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="10380-141">詳細については、「[インフラストラクチャの永続化層の設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と<xref:mvc/controllers/testing> 「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="10380-141">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="10380-142">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="10380-142">Test app organization</span></span>

<span data-ttu-id="10380-143">テストアプリは、 *tests/RazorPagesTestSample*フォルダー内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="10380-143">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="10380-144">テストアプリフォルダー</span><span class="sxs-lookup"><span data-stu-id="10380-144">Test app folder</span></span> | <span data-ttu-id="10380-145">説明</span><span class="sxs-lookup"><span data-stu-id="10380-145">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="10380-146">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="10380-146">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="10380-147">*DataAccessLayerTest.cs*には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="10380-147">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="10380-148">*IndexPageTests.cs*には、インデックスページモデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="10380-148">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="10380-149">*ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="10380-149">*Utilities*</span></span>     | <span data-ttu-id="10380-150">各 DAL 単体テストの新しいデータベースコンテキストオプションを作成するために使用するメソッドが含まれています。これにより、各テストのベースライン条件にデータベースがリセットされます。`TestDbContextOptions`</span><span class="sxs-lookup"><span data-stu-id="10380-150">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="10380-151">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="10380-151">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="10380-152">オブジェクトモックフレームワークは[Moq](https://github.com/moq/moq4)です。</span><span class="sxs-lookup"><span data-stu-id="10380-152">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="10380-153">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-153">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="10380-154">メッセージアプリには、 `AppDbContext`クラス (*src/RazorPagesTestSample/Data/appdbcontext .cs*) に含まれる4つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="10380-154">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="10380-155">各メソッドには、テストアプリに1つまたは2つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="10380-155">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="10380-156">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="10380-156">DAL method</span></span>               | <span data-ttu-id="10380-157">関数</span><span class="sxs-lookup"><span data-stu-id="10380-157">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="10380-158">プロパティによって並べ替えられたデータベースからを`List<Message>`取得します。 `Text`</span><span class="sxs-lookup"><span data-stu-id="10380-158">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="10380-159">を`Message`データベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="10380-159">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="10380-160">データベースから`Message`すべてのエントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-160">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="10380-161">によって`Message` `Id`、1つのをデータベースから削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-161">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="10380-162">DAL の単体テストでは<xref:Microsoft.EntityFrameworkCore.DbContextOptions> 、各テストに`AppDbContext`新しいを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="10380-162">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="10380-163">各テスト`DbContextOptions`のを作成する方法の1つは、 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>を使用することです。</span><span class="sxs-lookup"><span data-stu-id="10380-163">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="10380-164">この方法の問題は、各テストが、前のテストでどのような状態でデータベースを受け取るかということです。</span><span class="sxs-lookup"><span data-stu-id="10380-164">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="10380-165">相互に干渉しない atomic 単体テストを作成しようとすると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="10380-165">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="10380-166">が`AppDbContext`各テストに新しいデータベースコンテキストを使用するように強制するに`DbContextOptions`は、新しいサービスプロバイダーに基づくインスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="10380-166">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="10380-167">テストアプリでは、 `Utilities`クラスメソッド`TestDbContextOptions`を使用してこれを行う方法を示しています (tests/*RazorPagesTestSample/utilities/* utilities)。</span><span class="sxs-lookup"><span data-stu-id="10380-167">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="10380-168">DAL 単体`DbContextOptions`テストでを使用すると、新しいデータベースインスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="10380-168">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="10380-169">`DataAccessLayerTest`クラス (*unittests/dataaccessレイヤーテスト .cs*) の各テストメソッドは、次のように同様の配置/操作アサートパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="10380-169">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="10380-170">名簿データベースがテスト用に構成されているか、予期される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="10380-170">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="10380-171">作業テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="10380-171">Act: The test is executed.</span></span>
1. <span data-ttu-id="10380-172">アサートアサーションは、テスト結果が成功であるかどうかを判断するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="10380-172">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="10380-173">たとえば、 `DeleteMessageAsync`メソッドは、 `Id` (*src/RazorPagesTestSample/Data/appdbcontext .cs*) によって識別される1つのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-173">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="10380-174">このメソッドには2つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="10380-174">There are two tests for this method.</span></span> <span data-ttu-id="10380-175">1つのテストでは、メッセージがデータベース内に存在する場合に、メソッドによってメッセージが削除されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="10380-175">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="10380-176">もう1つの方法では、削除するメッセージ`Id`が存在しない場合にデータベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="10380-176">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="10380-177">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="10380-177">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="10380-178">まず、メソッドは、Act ステップの準備が行われる配置手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="10380-178">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="10380-179">シード処理メッセージは、で`seedMessages`取得および保持されます。</span><span class="sxs-lookup"><span data-stu-id="10380-179">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="10380-180">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="10380-180">The seeding messages are saved into the database.</span></span> <span data-ttu-id="10380-181">`Id` の`1`を持つメッセージは、削除の対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="10380-181">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="10380-182">メソッドが実行されるとき、予期されるメッセージには、 `Id`のがの`1`メッセージを除くすべてのメッセージが含まれている必要があります。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="10380-182">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="10380-183">変数`expectedMessages`は、この予想される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="10380-183">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="10380-184">メソッドは次のように動作します。メソッドは、の`recId` `1`を渡して実行されます。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="10380-184">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="10380-185">最後に、メソッドはコンテキスト`Messages`からを取得し、2つの`expectedMessages`が等しいことをアサートと比較します。</span><span class="sxs-lookup"><span data-stu-id="10380-185">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="10380-186">2つ`List<Message>`が同じであることを比較するために、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="10380-186">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="10380-187">メッセージはによって`Id`並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="10380-187">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="10380-188">メッセージのペアは、 `Text`プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="10380-188">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="10380-189">同様のテストメソッドは`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="10380-189">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="10380-190">この場合、データベース内の予期されるメッセージは、 `DeleteMessageAsync`メソッドが実行された後の実際のメッセージと同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="10380-190">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="10380-191">データベースのコンテンツを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="10380-191">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="10380-192">ページモデルメソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-192">Unit tests of the page model methods</span></span>

<span data-ttu-id="10380-193">別の単体テストのセットは、ページモデルメソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="10380-193">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="10380-194">メッセージアプリでは、インデックスページモデルは、 `IndexModel` *src/RazorPagesTestSample/Pages/index. cshtml. .cs*のクラスにあります。</span><span class="sxs-lookup"><span data-stu-id="10380-194">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="10380-195">ページモデルメソッド</span><span class="sxs-lookup"><span data-stu-id="10380-195">Page model method</span></span> | <span data-ttu-id="10380-196">関数</span><span class="sxs-lookup"><span data-stu-id="10380-196">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="10380-197">`GetMessagesAsync`メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="10380-197">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="10380-198">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が有効な場合、は`AddMessageAsync`を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="10380-198">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="10380-199">を`DeleteAllMessagesAsync`呼び出して、データベース内のすべてのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-199">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="10380-200">を`DeleteMessageAsync`実行して、 `Id`指定したを持つメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-200">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="10380-201">1つ以上のメッセージがデータベースに含まれている場合は、によって、メッセージあたりの平均単語数が計算されます。</span><span class="sxs-lookup"><span data-stu-id="10380-201">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="10380-202">ページモデルメソッドは、 `IndexPageTests`クラスで7つのテストを使用してテストされます (*テスト/RazorPagesTestSample/unittests/indexpagetests .cs*)。</span><span class="sxs-lookup"><span data-stu-id="10380-202">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="10380-203">テストでは、使い慣れた配置/アサートパターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="10380-203">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="10380-204">これらのテストは次のことに焦点を当てます。</span><span class="sxs-lookup"><span data-stu-id="10380-204">These tests focus on:</span></span>

* <span data-ttu-id="10380-205">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="10380-205">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="10380-206">メソッドによって正しい<xref:Microsoft.AspNetCore.Mvc.IActionResult>が生成されることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="10380-206">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="10380-207">プロパティ値の割り当てが正しく行われていることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="10380-207">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="10380-208">この一連のテストでは、多くの場合、ページモデルメソッドを実行する Act ステップに対して予期されるデータを生成するために DAL のメソッドがモックされます。</span><span class="sxs-lookup"><span data-stu-id="10380-208">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="10380-209">たとえば`GetMessagesAsync` 、`AppDbContext`のメソッドは、出力を生成するモックです。</span><span class="sxs-lookup"><span data-stu-id="10380-209">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="10380-210">ページモデルメソッドがこのメソッドを実行すると、モックは結果を返します。</span><span class="sxs-lookup"><span data-stu-id="10380-210">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="10380-211">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="10380-211">The data doesn't come from the database.</span></span> <span data-ttu-id="10380-212">これにより、ページモデルテストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="10380-212">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="10380-213">テスト`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`では、ページ`GetMessagesAsync`モデルに対してメソッドがモックされる方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="10380-213">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="10380-214">Act ステップで`GetMessagesAsync` メソッドを実行すると、ページモデルのメソッドが`OnGetAsync`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="10380-214">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="10380-215">単体テストの Act ステップ (*tests/RazorPagesTestSample/unittests/IndexPageTests .cs*):</span><span class="sxs-lookup"><span data-stu-id="10380-215">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="10380-216">`IndexPage`ページモデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index. cshtml. .cs*):</span><span class="sxs-lookup"><span data-stu-id="10380-216">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="10380-217">DAL `GetMessagesAsync`のメソッドは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="10380-217">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="10380-218">メソッドのモックバージョンは、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="10380-218">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="10380-219">この手順では、ページモデルの`actualMessages` `Messages`プロパティから実際のメッセージ () が割り当てられます。 `Assert`</span><span class="sxs-lookup"><span data-stu-id="10380-219">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="10380-220">型チェックは、メッセージが割り当てられたときにも実行されます。</span><span class="sxs-lookup"><span data-stu-id="10380-220">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="10380-221">予想されるメッセージと実際のメッセージは`Text` 、プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="10380-221">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="10380-222">テストでは、2つ`List<Message>`のインスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="10380-222">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="10380-223">このグループの他のテストでは、、、および<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>を確立<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `PageContext`するためのを含むページモデルオブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="10380-223">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="10380-224">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="10380-224">These are useful in conducting tests.</span></span> <span data-ttu-id="10380-225">たとえば、メッセージアプリは、の実行`ModelState`時`OnPostAddMessageAsync`に<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>有効<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>なが返されることを確認するために、でエラーを設定します。</span><span class="sxs-lookup"><span data-stu-id="10380-225">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="10380-226">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="10380-226">Additional resources</span></span>

* [<span data-ttu-id="10380-227">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-227">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="10380-228">[コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="10380-228">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="10380-229">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="10380-229">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="10380-230">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="10380-230">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="10380-231">XUnit.net の概要:.Net Core と .NET SDK コマンドラインの使用</span><span class="sxs-lookup"><span data-stu-id="10380-231">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="10380-232">Moq</span><span class="sxs-lookup"><span data-stu-id="10380-232">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="10380-233">Moq クイックスタート</span><span class="sxs-lookup"><span data-stu-id="10380-233">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10380-234">ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。</span><span class="sxs-lookup"><span data-stu-id="10380-234">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="10380-235">データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。</span><span class="sxs-lookup"><span data-stu-id="10380-235">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="10380-236">Razor Pages アプリの一部は、アプリの構築時に個別に1つの単位として機能します。</span><span class="sxs-lookup"><span data-stu-id="10380-236">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="10380-237">クラスとメソッドには、限られた範囲の責任があります。</span><span class="sxs-lookup"><span data-stu-id="10380-237">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="10380-238">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="10380-238">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="10380-239">コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。</span><span class="sxs-lookup"><span data-stu-id="10380-239">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="10380-240">このトピックでは、Razor Pages アプリと単体テストについての基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="10380-240">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="10380-241">Razor Pages アプリまたはテストの概念に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="10380-241">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="10380-242">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-242">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="10380-243">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="10380-243">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="10380-244">サンプルプロジェクトは、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="10380-244">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="10380-245">アプリ</span><span class="sxs-lookup"><span data-stu-id="10380-245">App</span></span>         | <span data-ttu-id="10380-246">プロジェクトフォルダー</span><span class="sxs-lookup"><span data-stu-id="10380-246">Project folder</span></span>                     | <span data-ttu-id="10380-247">説明</span><span class="sxs-lookup"><span data-stu-id="10380-247">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="10380-248">メッセージアプリ</span><span class="sxs-lookup"><span data-stu-id="10380-248">Message app</span></span> | <span data-ttu-id="10380-249">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="10380-249">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="10380-250">ユーザーがメッセージの追加、1つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析を実行できるようにします (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="10380-250">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="10380-251">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="10380-251">Test app</span></span>    | <span data-ttu-id="10380-252">*テスト/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="10380-252">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="10380-253">メッセージアプリの DAL および Index ページモデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="10380-253">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="10380-254">テストは、 [Visual Studio](/visualstudio/test/unit-test-your-code)や[VISUAL STUDIO FOR MAC](/dotnet/core/tutorials/using-on-mac-vs-full-solution)など、IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="10380-254">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="10380-255">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、Test */RazorPagesTestSample*フォルダーでコマンドプロンプトから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="10380-255">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="10380-256">メッセージアプリの組織</span><span class="sxs-lookup"><span data-stu-id="10380-256">Message app organization</span></span>

<span data-ttu-id="10380-257">メッセージアプリは、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="10380-257">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="10380-258">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="10380-258">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="10380-259">メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="10380-259">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="10380-260">`Text`プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="10380-260">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="10380-261">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="10380-261">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="10380-262">アプリには、データベースコンテキストクラス`AppDbContext` (*Data/appdbcontext .cs*) に DAL が含まれています。</span><span class="sxs-lookup"><span data-stu-id="10380-262">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="10380-263">DAL メソッドはとマーク`virtual`されます。これにより、テストで使用するメソッドをモックできます。</span><span class="sxs-lookup"><span data-stu-id="10380-263">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="10380-264">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="10380-264">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="10380-265">これらのシード処理された*メッセージ*は、テストでも使用されます。</span><span class="sxs-lookup"><span data-stu-id="10380-265">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="10380-266">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="10380-266">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="10380-267">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="10380-267">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="10380-268">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="10380-268">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="10380-269">サンプルアプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="10380-269">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="10380-270">詳細については、「[インフラストラクチャの永続化層の設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と<xref:mvc/controllers/testing> 「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="10380-270">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="10380-271">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="10380-271">Test app organization</span></span>

<span data-ttu-id="10380-272">テストアプリは、 *tests/RazorPagesTestSample*フォルダー内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="10380-272">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="10380-273">テストアプリフォルダー</span><span class="sxs-lookup"><span data-stu-id="10380-273">Test app folder</span></span> | <span data-ttu-id="10380-274">説明</span><span class="sxs-lookup"><span data-stu-id="10380-274">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="10380-275">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="10380-275">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="10380-276">*DataAccessLayerTest.cs*には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="10380-276">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="10380-277">*IndexPageTests.cs*には、インデックスページモデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="10380-277">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="10380-278">*ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="10380-278">*Utilities*</span></span>     | <span data-ttu-id="10380-279">各 DAL 単体テストの新しいデータベースコンテキストオプションを作成するために使用するメソッドが含まれています。これにより、各テストのベースライン条件にデータベースがリセットされます。`TestDbContextOptions`</span><span class="sxs-lookup"><span data-stu-id="10380-279">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="10380-280">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="10380-280">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="10380-281">オブジェクトモックフレームワークは[Moq](https://github.com/moq/moq4)です。</span><span class="sxs-lookup"><span data-stu-id="10380-281">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="10380-282">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-282">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="10380-283">メッセージアプリには、 `AppDbContext`クラス (*src/RazorPagesTestSample/Data/appdbcontext .cs*) に含まれる4つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="10380-283">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="10380-284">各メソッドには、テストアプリに1つまたは2つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="10380-284">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="10380-285">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="10380-285">DAL method</span></span>               | <span data-ttu-id="10380-286">関数</span><span class="sxs-lookup"><span data-stu-id="10380-286">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="10380-287">プロパティによって並べ替えられたデータベースからを`List<Message>`取得します。 `Text`</span><span class="sxs-lookup"><span data-stu-id="10380-287">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="10380-288">を`Message`データベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="10380-288">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="10380-289">データベースから`Message`すべてのエントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-289">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="10380-290">によって`Message` `Id`、1つのをデータベースから削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-290">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="10380-291">DAL の単体テストでは<xref:Microsoft.EntityFrameworkCore.DbContextOptions> 、各テストに`AppDbContext`新しいを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="10380-291">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="10380-292">各テスト`DbContextOptions`のを作成する方法の1つは、 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>を使用することです。</span><span class="sxs-lookup"><span data-stu-id="10380-292">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="10380-293">この方法の問題は、各テストが、前のテストでどのような状態でデータベースを受け取るかということです。</span><span class="sxs-lookup"><span data-stu-id="10380-293">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="10380-294">相互に干渉しない atomic 単体テストを作成しようとすると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="10380-294">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="10380-295">が`AppDbContext`各テストに新しいデータベースコンテキストを使用するように強制するに`DbContextOptions`は、新しいサービスプロバイダーに基づくインスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="10380-295">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="10380-296">テストアプリでは、 `Utilities`クラスメソッド`TestDbContextOptions`を使用してこれを行う方法を示しています (tests/*RazorPagesTestSample/utilities/* utilities)。</span><span class="sxs-lookup"><span data-stu-id="10380-296">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="10380-297">DAL 単体`DbContextOptions`テストでを使用すると、新しいデータベースインスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="10380-297">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="10380-298">`DataAccessLayerTest`クラス (*unittests/dataaccessレイヤーテスト .cs*) の各テストメソッドは、次のように同様の配置/操作アサートパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="10380-298">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="10380-299">名簿データベースがテスト用に構成されているか、予期される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="10380-299">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="10380-300">作業テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="10380-300">Act: The test is executed.</span></span>
1. <span data-ttu-id="10380-301">アサートアサーションは、テスト結果が成功であるかどうかを判断するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="10380-301">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="10380-302">たとえば、 `DeleteMessageAsync`メソッドは、 `Id` (*src/RazorPagesTestSample/Data/appdbcontext .cs*) によって識別される1つのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-302">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="10380-303">このメソッドには2つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="10380-303">There are two tests for this method.</span></span> <span data-ttu-id="10380-304">1つのテストでは、メッセージがデータベース内に存在する場合に、メソッドによってメッセージが削除されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="10380-304">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="10380-305">もう1つの方法では、削除するメッセージ`Id`が存在しない場合にデータベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="10380-305">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="10380-306">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="10380-306">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="10380-307">まず、メソッドは、Act ステップの準備が行われる配置手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="10380-307">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="10380-308">シード処理メッセージは、で`seedMessages`取得および保持されます。</span><span class="sxs-lookup"><span data-stu-id="10380-308">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="10380-309">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="10380-309">The seeding messages are saved into the database.</span></span> <span data-ttu-id="10380-310">`Id` の`1`を持つメッセージは、削除の対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="10380-310">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="10380-311">メソッドが実行されるとき、予期されるメッセージには、 `Id`のがの`1`メッセージを除くすべてのメッセージが含まれている必要があります。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="10380-311">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="10380-312">変数`expectedMessages`は、この予想される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="10380-312">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="10380-313">メソッドは次のように動作します。メソッドは、の`recId` `1`を渡して実行されます。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="10380-313">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="10380-314">最後に、メソッドはコンテキスト`Messages`からを取得し、2つの`expectedMessages`が等しいことをアサートと比較します。</span><span class="sxs-lookup"><span data-stu-id="10380-314">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="10380-315">2つ`List<Message>`が同じであることを比較するために、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="10380-315">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="10380-316">メッセージはによって`Id`並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="10380-316">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="10380-317">メッセージのペアは、 `Text`プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="10380-317">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="10380-318">同様のテストメソッドは`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="10380-318">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="10380-319">この場合、データベース内の予期されるメッセージは、 `DeleteMessageAsync`メソッドが実行された後の実際のメッセージと同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="10380-319">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="10380-320">データベースのコンテンツを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="10380-320">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="10380-321">ページモデルメソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-321">Unit tests of the page model methods</span></span>

<span data-ttu-id="10380-322">別の単体テストのセットは、ページモデルメソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="10380-322">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="10380-323">メッセージアプリでは、インデックスページモデルは、 `IndexModel` *src/RazorPagesTestSample/Pages/index. cshtml. .cs*のクラスにあります。</span><span class="sxs-lookup"><span data-stu-id="10380-323">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="10380-324">ページモデルメソッド</span><span class="sxs-lookup"><span data-stu-id="10380-324">Page model method</span></span> | <span data-ttu-id="10380-325">関数</span><span class="sxs-lookup"><span data-stu-id="10380-325">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="10380-326">`GetMessagesAsync`メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="10380-326">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="10380-327">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が有効な場合、は`AddMessageAsync`を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="10380-327">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="10380-328">を`DeleteAllMessagesAsync`呼び出して、データベース内のすべてのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-328">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="10380-329">を`DeleteMessageAsync`実行して、 `Id`指定したを持つメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="10380-329">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="10380-330">1つ以上のメッセージがデータベースに含まれている場合は、によって、メッセージあたりの平均単語数が計算されます。</span><span class="sxs-lookup"><span data-stu-id="10380-330">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="10380-331">ページモデルメソッドは、 `IndexPageTests`クラスで7つのテストを使用してテストされます (*テスト/RazorPagesTestSample/unittests/indexpagetests .cs*)。</span><span class="sxs-lookup"><span data-stu-id="10380-331">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="10380-332">テストでは、使い慣れた配置/アサートパターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="10380-332">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="10380-333">これらのテストは次のことに焦点を当てます。</span><span class="sxs-lookup"><span data-stu-id="10380-333">These tests focus on:</span></span>

* <span data-ttu-id="10380-334">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="10380-334">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="10380-335">メソッドによって正しい<xref:Microsoft.AspNetCore.Mvc.IActionResult>が生成されることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="10380-335">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="10380-336">プロパティ値の割り当てが正しく行われていることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="10380-336">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="10380-337">この一連のテストでは、多くの場合、ページモデルメソッドを実行する Act ステップに対して予期されるデータを生成するために DAL のメソッドがモックされます。</span><span class="sxs-lookup"><span data-stu-id="10380-337">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="10380-338">たとえば`GetMessagesAsync` 、`AppDbContext`のメソッドは、出力を生成するモックです。</span><span class="sxs-lookup"><span data-stu-id="10380-338">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="10380-339">ページモデルメソッドがこのメソッドを実行すると、モックは結果を返します。</span><span class="sxs-lookup"><span data-stu-id="10380-339">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="10380-340">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="10380-340">The data doesn't come from the database.</span></span> <span data-ttu-id="10380-341">これにより、ページモデルテストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="10380-341">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="10380-342">テスト`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`では、ページ`GetMessagesAsync`モデルに対してメソッドがモックされる方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="10380-342">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="10380-343">Act ステップで`GetMessagesAsync` メソッドを実行すると、ページモデルのメソッドが`OnGetAsync`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="10380-343">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="10380-344">単体テストの Act ステップ (*tests/RazorPagesTestSample/unittests/IndexPageTests .cs*):</span><span class="sxs-lookup"><span data-stu-id="10380-344">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="10380-345">`IndexPage`ページモデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index. cshtml. .cs*):</span><span class="sxs-lookup"><span data-stu-id="10380-345">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="10380-346">DAL `GetMessagesAsync`のメソッドは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="10380-346">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="10380-347">メソッドのモックバージョンは、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="10380-347">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="10380-348">この手順では、ページモデルの`actualMessages` `Messages`プロパティから実際のメッセージ () が割り当てられます。 `Assert`</span><span class="sxs-lookup"><span data-stu-id="10380-348">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="10380-349">型チェックは、メッセージが割り当てられたときにも実行されます。</span><span class="sxs-lookup"><span data-stu-id="10380-349">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="10380-350">予想されるメッセージと実際のメッセージは`Text` 、プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="10380-350">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="10380-351">テストでは、2つ`List<Message>`のインスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="10380-351">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="10380-352">このグループの他のテストでは、、、および<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>を確立<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `PageContext`するためのを含むページモデルオブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="10380-352">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="10380-353">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="10380-353">These are useful in conducting tests.</span></span> <span data-ttu-id="10380-354">たとえば、メッセージアプリは、の実行`ModelState`時`OnPostAddMessageAsync`に<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>有効<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>なが返されることを確認するために、でエラーを設定します。</span><span class="sxs-lookup"><span data-stu-id="10380-354">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="10380-355">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="10380-355">Additional resources</span></span>

* [<span data-ttu-id="10380-356">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="10380-356">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="10380-357">[コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="10380-357">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="10380-358">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="10380-358">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="10380-359">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="10380-359">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="10380-360">XUnit.net の概要:.Net Core と .NET SDK コマンドラインの使用</span><span class="sxs-lookup"><span data-stu-id="10380-360">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="10380-361">Moq</span><span class="sxs-lookup"><span data-stu-id="10380-361">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="10380-362">Moq クイックスタート</span><span class="sxs-lookup"><span data-stu-id="10380-362">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
