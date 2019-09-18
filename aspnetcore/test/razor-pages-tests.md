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
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="5d38f-103">ASP.NET Core での単体テストの Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5d38f-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="5d38f-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5d38f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d38f-105">ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="5d38f-106">データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="5d38f-107">Razor Pages アプリの一部は、アプリの構築時に個別に1つの単位として機能します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="5d38f-108">クラスとメソッドには、限られた範囲の責任があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="5d38f-109">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="5d38f-110">コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。</span><span class="sxs-lookup"><span data-stu-id="5d38f-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="5d38f-111">このトピックでは、Razor Pages アプリと単体テストについての基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="5d38f-112">Razor Pages アプリまたはテストの概念に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d38f-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="5d38f-113">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-113">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="5d38f-114">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5d38f-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5d38f-115">サンプルプロジェクトは、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-115">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="5d38f-116">アプリ</span><span class="sxs-lookup"><span data-stu-id="5d38f-116">App</span></span>         | <span data-ttu-id="5d38f-117">プロジェクトフォルダー</span><span class="sxs-lookup"><span data-stu-id="5d38f-117">Project folder</span></span>                     | <span data-ttu-id="5d38f-118">説明</span><span class="sxs-lookup"><span data-stu-id="5d38f-118">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="5d38f-119">メッセージアプリ</span><span class="sxs-lookup"><span data-stu-id="5d38f-119">Message app</span></span> | <span data-ttu-id="5d38f-120">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="5d38f-120">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="5d38f-121">ユーザーがメッセージの追加、1つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析を実行できるようにします (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-121">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="5d38f-122">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-122">Test app</span></span>    | <span data-ttu-id="5d38f-123">*テスト/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="5d38f-123">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="5d38f-124">メッセージアプリの DAL および Index ページモデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-124">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="5d38f-125">テストは、 [Visual Studio](/visualstudio/test/unit-test-your-code)や[VISUAL STUDIO FOR MAC](/dotnet/core/tutorials/using-on-mac-vs-full-solution)など、IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-125">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="5d38f-126">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、Test */RazorPagesTestSample*フォルダーでコマンドプロンプトから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-126">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="5d38f-127">メッセージアプリの組織</span><span class="sxs-lookup"><span data-stu-id="5d38f-127">Message app organization</span></span>

<span data-ttu-id="5d38f-128">メッセージアプリは、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-128">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="5d38f-129">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-129">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="5d38f-130">メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-130">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="5d38f-131">`Text`プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-131">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="5d38f-132">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-132">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="5d38f-133">アプリには、データベースコンテキストクラス`AppDbContext` (*Data/appdbcontext .cs*) に DAL が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-133">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="5d38f-134">DAL メソッドはとマーク`virtual`されます。これにより、テストで使用するメソッドをモックできます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-134">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="5d38f-135">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-135">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="5d38f-136">これらのシード処理された*メッセージ*は、テストでも使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-136">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="5d38f-137">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-137">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="5d38f-138">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-138">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="5d38f-139">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-139">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="5d38f-140">サンプルアプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-140">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="5d38f-141">詳細については、「[インフラストラクチャの永続化層の設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と<xref:mvc/controllers/testing> 「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d38f-141">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="5d38f-142">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="5d38f-142">Test app organization</span></span>

<span data-ttu-id="5d38f-143">テストアプリは、 *tests/RazorPagesTestSample*フォルダー内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-143">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="5d38f-144">テストアプリフォルダー</span><span class="sxs-lookup"><span data-stu-id="5d38f-144">Test app folder</span></span> | <span data-ttu-id="5d38f-145">説明</span><span class="sxs-lookup"><span data-stu-id="5d38f-145">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="5d38f-146">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="5d38f-146">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="5d38f-147">*DataAccessLayerTest.cs*には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-147">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="5d38f-148">*IndexPageTests.cs*には、インデックスページモデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-148">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="5d38f-149">*ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="5d38f-149">*Utilities*</span></span>     | <span data-ttu-id="5d38f-150">各 DAL 単体テストの新しいデータベースコンテキストオプションを作成するために使用するメソッドが含まれています。これにより、各テストのベースライン条件にデータベースがリセットされます。`TestDbContextOptions`</span><span class="sxs-lookup"><span data-stu-id="5d38f-150">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="5d38f-151">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="5d38f-151">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="5d38f-152">オブジェクトモックフレームワークは[Moq](https://github.com/moq/moq4)です。</span><span class="sxs-lookup"><span data-stu-id="5d38f-152">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="5d38f-153">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-153">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="5d38f-154">メッセージアプリには、 `AppDbContext`クラス (*src/RazorPagesTestSample/Data/appdbcontext .cs*) に含まれる4つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-154">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="5d38f-155">各メソッドには、テストアプリに1つまたは2つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-155">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="5d38f-156">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="5d38f-156">DAL method</span></span>               | <span data-ttu-id="5d38f-157">関数</span><span class="sxs-lookup"><span data-stu-id="5d38f-157">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="5d38f-158">プロパティによって並べ替えられたデータベースからを`List<Message>`取得します。 `Text`</span><span class="sxs-lookup"><span data-stu-id="5d38f-158">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="5d38f-159">を`Message`データベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-159">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="5d38f-160">データベースから`Message`すべてのエントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-160">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="5d38f-161">によって`Message` `Id`、1つのをデータベースから削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-161">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="5d38f-162">DAL の単体テストでは<xref:Microsoft.EntityFrameworkCore.DbContextOptions> 、各テストに`AppDbContext`新しいを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-162">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="5d38f-163">各テスト`DbContextOptions`のを作成する方法の1つは、 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>を使用することです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-163">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="5d38f-164">この方法の問題は、各テストが、前のテストでどのような状態でデータベースを受け取るかということです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-164">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="5d38f-165">相互に干渉しない atomic 単体テストを作成しようとすると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-165">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="5d38f-166">が`AppDbContext`各テストに新しいデータベースコンテキストを使用するように強制するに`DbContextOptions`は、新しいサービスプロバイダーに基づくインスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-166">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="5d38f-167">テストアプリでは、 `Utilities`クラスメソッド`TestDbContextOptions`を使用してこれを行う方法を示しています (tests/*RazorPagesTestSample/utilities/* utilities)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-167">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="5d38f-168">DAL 単体`DbContextOptions`テストでを使用すると、新しいデータベースインスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-168">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="5d38f-169">`DataAccessLayerTest`クラス (*unittests/dataaccessレイヤーテスト .cs*) の各テストメソッドは、次のように同様の配置/操作アサートパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="5d38f-169">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="5d38f-170">名簿データベースがテスト用に構成されているか、予期される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-170">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="5d38f-171">作業テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-171">Act: The test is executed.</span></span>
1. <span data-ttu-id="5d38f-172">アサートアサーションは、テスト結果が成功であるかどうかを判断するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-172">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="5d38f-173">たとえば、 `DeleteMessageAsync`メソッドは、 `Id` (*src/RazorPagesTestSample/Data/appdbcontext .cs*) によって識別される1つのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-173">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="5d38f-174">このメソッドには2つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-174">There are two tests for this method.</span></span> <span data-ttu-id="5d38f-175">1つのテストでは、メッセージがデータベース内に存在する場合に、メソッドによってメッセージが削除されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-175">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="5d38f-176">もう1つの方法では、削除するメッセージ`Id`が存在しない場合にデータベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-176">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="5d38f-177">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-177">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="5d38f-178">まず、メソッドは、Act ステップの準備が行われる配置手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-178">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="5d38f-179">シード処理メッセージは、で`seedMessages`取得および保持されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-179">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="5d38f-180">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-180">The seeding messages are saved into the database.</span></span> <span data-ttu-id="5d38f-181">`Id` の`1`を持つメッセージは、削除の対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-181">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="5d38f-182">メソッドが実行されるとき、予期されるメッセージには、 `Id`のがの`1`メッセージを除くすべてのメッセージが含まれている必要があります。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="5d38f-182">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="5d38f-183">変数`expectedMessages`は、この予想される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-183">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="5d38f-184">メソッドは次のように動作します。メソッドは、の`recId` `1`を渡して実行されます。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="5d38f-184">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="5d38f-185">最後に、メソッドはコンテキスト`Messages`からを取得し、2つの`expectedMessages`が等しいことをアサートと比較します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-185">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="5d38f-186">2つ`List<Message>`が同じであることを比較するために、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-186">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="5d38f-187">メッセージはによって`Id`並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-187">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="5d38f-188">メッセージのペアは、 `Text`プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-188">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="5d38f-189">同様のテストメソッドは`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-189">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="5d38f-190">この場合、データベース内の予期されるメッセージは、 `DeleteMessageAsync`メソッドが実行された後の実際のメッセージと同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-190">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="5d38f-191">データベースのコンテンツを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-191">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="5d38f-192">ページモデルメソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-192">Unit tests of the page model methods</span></span>

<span data-ttu-id="5d38f-193">別の単体テストのセットは、ページモデルメソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-193">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="5d38f-194">メッセージアプリでは、インデックスページモデルは、 `IndexModel` *src/RazorPagesTestSample/Pages/index. cshtml. .cs*のクラスにあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-194">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="5d38f-195">ページモデルメソッド</span><span class="sxs-lookup"><span data-stu-id="5d38f-195">Page model method</span></span> | <span data-ttu-id="5d38f-196">関数</span><span class="sxs-lookup"><span data-stu-id="5d38f-196">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="5d38f-197">`GetMessagesAsync`メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-197">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="5d38f-198">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が有効な場合、は`AddMessageAsync`を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-198">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="5d38f-199">を`DeleteAllMessagesAsync`呼び出して、データベース内のすべてのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-199">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="5d38f-200">を`DeleteMessageAsync`実行して、 `Id`指定したを持つメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-200">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="5d38f-201">1つ以上のメッセージがデータベースに含まれている場合は、によって、メッセージあたりの平均単語数が計算されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-201">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="5d38f-202">ページモデルメソッドは、 `IndexPageTests`クラスで7つのテストを使用してテストされます (*テスト/RazorPagesTestSample/unittests/indexpagetests .cs*)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-202">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="5d38f-203">テストでは、使い慣れた配置/アサートパターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-203">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="5d38f-204">これらのテストは次のことに焦点を当てます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-204">These tests focus on:</span></span>

* <span data-ttu-id="5d38f-205">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-205">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="5d38f-206">メソッドによって正しい<xref:Microsoft.AspNetCore.Mvc.IActionResult>が生成されることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-206">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="5d38f-207">プロパティ値の割り当てが正しく行われていることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-207">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="5d38f-208">この一連のテストでは、多くの場合、ページモデルメソッドを実行する Act ステップに対して予期されるデータを生成するために DAL のメソッドがモックされます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-208">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="5d38f-209">たとえば`GetMessagesAsync` 、`AppDbContext`のメソッドは、出力を生成するモックです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-209">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="5d38f-210">ページモデルメソッドがこのメソッドを実行すると、モックは結果を返します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-210">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="5d38f-211">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-211">The data doesn't come from the database.</span></span> <span data-ttu-id="5d38f-212">これにより、ページモデルテストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-212">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="5d38f-213">テスト`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`では、ページ`GetMessagesAsync`モデルに対してメソッドがモックされる方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-213">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="5d38f-214">Act ステップで`GetMessagesAsync` メソッドを実行すると、ページモデルのメソッドが`OnGetAsync`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-214">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="5d38f-215">単体テストの Act ステップ (*tests/RazorPagesTestSample/unittests/IndexPageTests .cs*):</span><span class="sxs-lookup"><span data-stu-id="5d38f-215">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="5d38f-216">`IndexPage`ページモデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index. cshtml. .cs*):</span><span class="sxs-lookup"><span data-stu-id="5d38f-216">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="5d38f-217">DAL `GetMessagesAsync`のメソッドは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-217">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="5d38f-218">メソッドのモックバージョンは、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-218">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="5d38f-219">この手順では、ページモデルの`actualMessages` `Messages`プロパティから実際のメッセージ () が割り当てられます。 `Assert`</span><span class="sxs-lookup"><span data-stu-id="5d38f-219">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="5d38f-220">型チェックは、メッセージが割り当てられたときにも実行されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-220">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="5d38f-221">予想されるメッセージと実際のメッセージは`Text` 、プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-221">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="5d38f-222">テストでは、2つ`List<Message>`のインスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-222">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="5d38f-223">このグループの他のテストでは、、、および<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>を確立<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `PageContext`するためのを含むページモデルオブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-223">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="5d38f-224">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-224">These are useful in conducting tests.</span></span> <span data-ttu-id="5d38f-225">たとえば、メッセージアプリは、の実行`ModelState`時`OnPostAddMessageAsync`に<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>有効<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>なが返されることを確認するために、でエラーを設定します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-225">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="5d38f-226">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5d38f-226">Additional resources</span></span>

* [<span data-ttu-id="5d38f-227">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-227">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="5d38f-228">[コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="5d38f-228">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="5d38f-229">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="5d38f-229">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="5d38f-230">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="5d38f-230">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="5d38f-231">XUnit.net の概要:.Net Core と .NET SDK コマンドラインの使用</span><span class="sxs-lookup"><span data-stu-id="5d38f-231">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="5d38f-232">Moq</span><span class="sxs-lookup"><span data-stu-id="5d38f-232">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="5d38f-233">Moq クイックスタート</span><span class="sxs-lookup"><span data-stu-id="5d38f-233">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5d38f-234">ASP.NET Core では、Razor Pages のアプリの単体テストをサポートします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-234">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="5d38f-235">データ アクセス層 (DAL) のテストとページ モデルによって、次が保証されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-235">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="5d38f-236">Razor Pages アプリの一部は、アプリの構築時に個別に1つの単位として機能します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-236">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="5d38f-237">クラスとメソッドには、限られた範囲の責任があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-237">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="5d38f-238">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-238">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="5d38f-239">コードのアップデートによってもたらされたエラーである回帰は、自動ビルドと配置中に検出されました。</span><span class="sxs-lookup"><span data-stu-id="5d38f-239">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="5d38f-240">このトピックでは、Razor Pages アプリと単体テストについての基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-240">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="5d38f-241">Razor Pages アプリまたはテストの概念に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d38f-241">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="5d38f-242">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-242">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="5d38f-243">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5d38f-243">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5d38f-244">サンプルプロジェクトは、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-244">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="5d38f-245">アプリ</span><span class="sxs-lookup"><span data-stu-id="5d38f-245">App</span></span>         | <span data-ttu-id="5d38f-246">プロジェクトフォルダー</span><span class="sxs-lookup"><span data-stu-id="5d38f-246">Project folder</span></span>                     | <span data-ttu-id="5d38f-247">説明</span><span class="sxs-lookup"><span data-stu-id="5d38f-247">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="5d38f-248">メッセージアプリ</span><span class="sxs-lookup"><span data-stu-id="5d38f-248">Message app</span></span> | <span data-ttu-id="5d38f-249">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="5d38f-249">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="5d38f-250">ユーザーがメッセージの追加、1つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析を実行できるようにします (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-250">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="5d38f-251">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-251">Test app</span></span>    | <span data-ttu-id="5d38f-252">*テスト/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="5d38f-252">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="5d38f-253">メッセージアプリの DAL および Index ページモデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-253">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="5d38f-254">テストは、 [Visual Studio](/visualstudio/test/unit-test-your-code)や[VISUAL STUDIO FOR MAC](/dotnet/core/tutorials/using-on-mac-vs-full-solution)など、IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-254">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="5d38f-255">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、Test */RazorPagesTestSample*フォルダーでコマンドプロンプトから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-255">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="5d38f-256">メッセージアプリの組織</span><span class="sxs-lookup"><span data-stu-id="5d38f-256">Message app organization</span></span>

<span data-ttu-id="5d38f-257">メッセージアプリは、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-257">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="5d38f-258">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージあたりの平均単語数を検索します)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-258">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="5d38f-259">メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-259">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="5d38f-260">`Text`プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-260">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="5d38f-261">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-261">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="5d38f-262">アプリには、データベースコンテキストクラス`AppDbContext` (*Data/appdbcontext .cs*) に DAL が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-262">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="5d38f-263">DAL メソッドはとマーク`virtual`されます。これにより、テストで使用するメソッドをモックできます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-263">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="5d38f-264">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-264">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="5d38f-265">これらのシード処理された*メッセージ*は、テストでも使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-265">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="5d38f-266">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-266">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="5d38f-267">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-267">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="5d38f-268">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-268">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="5d38f-269">サンプルアプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-269">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="5d38f-270">詳細については、「[インフラストラクチャの永続化層の設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と<xref:mvc/controllers/testing> 「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d38f-270">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="5d38f-271">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="5d38f-271">Test app organization</span></span>

<span data-ttu-id="5d38f-272">テストアプリは、 *tests/RazorPagesTestSample*フォルダー内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-272">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="5d38f-273">テストアプリフォルダー</span><span class="sxs-lookup"><span data-stu-id="5d38f-273">Test app folder</span></span> | <span data-ttu-id="5d38f-274">説明</span><span class="sxs-lookup"><span data-stu-id="5d38f-274">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="5d38f-275">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="5d38f-275">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="5d38f-276">*DataAccessLayerTest.cs*には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-276">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="5d38f-277">*IndexPageTests.cs*には、インデックスページモデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-277">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="5d38f-278">*ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="5d38f-278">*Utilities*</span></span>     | <span data-ttu-id="5d38f-279">各 DAL 単体テストの新しいデータベースコンテキストオプションを作成するために使用するメソッドが含まれています。これにより、各テストのベースライン条件にデータベースがリセットされます。`TestDbContextOptions`</span><span class="sxs-lookup"><span data-stu-id="5d38f-279">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="5d38f-280">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="5d38f-280">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="5d38f-281">オブジェクトモックフレームワークは[Moq](https://github.com/moq/moq4)です。</span><span class="sxs-lookup"><span data-stu-id="5d38f-281">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="5d38f-282">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-282">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="5d38f-283">メッセージアプリには、 `AppDbContext`クラス (*src/RazorPagesTestSample/Data/appdbcontext .cs*) に含まれる4つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-283">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="5d38f-284">各メソッドには、テストアプリに1つまたは2つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-284">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="5d38f-285">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="5d38f-285">DAL method</span></span>               | <span data-ttu-id="5d38f-286">関数</span><span class="sxs-lookup"><span data-stu-id="5d38f-286">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="5d38f-287">プロパティによって並べ替えられたデータベースからを`List<Message>`取得します。 `Text`</span><span class="sxs-lookup"><span data-stu-id="5d38f-287">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="5d38f-288">を`Message`データベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-288">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="5d38f-289">データベースから`Message`すべてのエントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-289">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="5d38f-290">によって`Message` `Id`、1つのをデータベースから削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-290">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="5d38f-291">DAL の単体テストでは<xref:Microsoft.EntityFrameworkCore.DbContextOptions> 、各テストに`AppDbContext`新しいを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-291">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="5d38f-292">各テスト`DbContextOptions`のを作成する方法の1つは、 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>を使用することです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-292">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="5d38f-293">この方法の問題は、各テストが、前のテストでどのような状態でデータベースを受け取るかということです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-293">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="5d38f-294">相互に干渉しない atomic 単体テストを作成しようとすると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-294">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="5d38f-295">が`AppDbContext`各テストに新しいデータベースコンテキストを使用するように強制するに`DbContextOptions`は、新しいサービスプロバイダーに基づくインスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-295">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="5d38f-296">テストアプリでは、 `Utilities`クラスメソッド`TestDbContextOptions`を使用してこれを行う方法を示しています (tests/*RazorPagesTestSample/utilities/* utilities)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-296">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="5d38f-297">DAL 単体`DbContextOptions`テストでを使用すると、新しいデータベースインスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-297">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="5d38f-298">`DataAccessLayerTest`クラス (*unittests/dataaccessレイヤーテスト .cs*) の各テストメソッドは、次のように同様の配置/操作アサートパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="5d38f-298">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="5d38f-299">名簿データベースがテスト用に構成されているか、予期される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-299">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="5d38f-300">作業テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-300">Act: The test is executed.</span></span>
1. <span data-ttu-id="5d38f-301">アサートアサーションは、テスト結果が成功であるかどうかを判断するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-301">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="5d38f-302">たとえば、 `DeleteMessageAsync`メソッドは、 `Id` (*src/RazorPagesTestSample/Data/appdbcontext .cs*) によって識別される1つのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-302">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="5d38f-303">このメソッドには2つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-303">There are two tests for this method.</span></span> <span data-ttu-id="5d38f-304">1つのテストでは、メッセージがデータベース内に存在する場合に、メソッドによってメッセージが削除されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-304">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="5d38f-305">もう1つの方法では、削除するメッセージ`Id`が存在しない場合にデータベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-305">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="5d38f-306">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`メソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-306">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="5d38f-307">まず、メソッドは、Act ステップの準備が行われる配置手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-307">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="5d38f-308">シード処理メッセージは、で`seedMessages`取得および保持されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-308">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="5d38f-309">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-309">The seeding messages are saved into the database.</span></span> <span data-ttu-id="5d38f-310">`Id` の`1`を持つメッセージは、削除の対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-310">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="5d38f-311">メソッドが実行されるとき、予期されるメッセージには、 `Id`のがの`1`メッセージを除くすべてのメッセージが含まれている必要があります。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="5d38f-311">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="5d38f-312">変数`expectedMessages`は、この予想される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-312">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="5d38f-313">メソッドは次のように動作します。メソッドは、の`recId` `1`を渡して実行されます。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="5d38f-313">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="5d38f-314">最後に、メソッドはコンテキスト`Messages`からを取得し、2つの`expectedMessages`が等しいことをアサートと比較します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-314">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="5d38f-315">2つ`List<Message>`が同じであることを比較するために、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-315">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="5d38f-316">メッセージはによって`Id`並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-316">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="5d38f-317">メッセージのペアは、 `Text`プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-317">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="5d38f-318">同様のテストメソッドは`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-318">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="5d38f-319">この場合、データベース内の予期されるメッセージは、 `DeleteMessageAsync`メソッドが実行された後の実際のメッセージと同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-319">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="5d38f-320">データベースのコンテンツを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-320">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="5d38f-321">ページモデルメソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-321">Unit tests of the page model methods</span></span>

<span data-ttu-id="5d38f-322">別の単体テストのセットは、ページモデルメソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-322">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="5d38f-323">メッセージアプリでは、インデックスページモデルは、 `IndexModel` *src/RazorPagesTestSample/Pages/index. cshtml. .cs*のクラスにあります。</span><span class="sxs-lookup"><span data-stu-id="5d38f-323">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="5d38f-324">ページモデルメソッド</span><span class="sxs-lookup"><span data-stu-id="5d38f-324">Page model method</span></span> | <span data-ttu-id="5d38f-325">関数</span><span class="sxs-lookup"><span data-stu-id="5d38f-325">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="5d38f-326">`GetMessagesAsync`メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-326">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="5d38f-327">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が有効な場合、は`AddMessageAsync`を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-327">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="5d38f-328">を`DeleteAllMessagesAsync`呼び出して、データベース内のすべてのメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-328">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="5d38f-329">を`DeleteMessageAsync`実行して、 `Id`指定したを持つメッセージを削除します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-329">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="5d38f-330">1つ以上のメッセージがデータベースに含まれている場合は、によって、メッセージあたりの平均単語数が計算されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-330">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="5d38f-331">ページモデルメソッドは、 `IndexPageTests`クラスで7つのテストを使用してテストされます (*テスト/RazorPagesTestSample/unittests/indexpagetests .cs*)。</span><span class="sxs-lookup"><span data-stu-id="5d38f-331">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="5d38f-332">テストでは、使い慣れた配置/アサートパターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-332">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="5d38f-333">これらのテストは次のことに焦点を当てます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-333">These tests focus on:</span></span>

* <span data-ttu-id="5d38f-334">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-334">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="5d38f-335">メソッドによって正しい<xref:Microsoft.AspNetCore.Mvc.IActionResult>が生成されることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-335">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="5d38f-336">プロパティ値の割り当てが正しく行われていることを確認しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-336">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="5d38f-337">この一連のテストでは、多くの場合、ページモデルメソッドを実行する Act ステップに対して予期されるデータを生成するために DAL のメソッドがモックされます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-337">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="5d38f-338">たとえば`GetMessagesAsync` 、`AppDbContext`のメソッドは、出力を生成するモックです。</span><span class="sxs-lookup"><span data-stu-id="5d38f-338">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="5d38f-339">ページモデルメソッドがこのメソッドを実行すると、モックは結果を返します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-339">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="5d38f-340">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-340">The data doesn't come from the database.</span></span> <span data-ttu-id="5d38f-341">これにより、ページモデルテストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-341">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="5d38f-342">テスト`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`では、ページ`GetMessagesAsync`モデルに対してメソッドがモックされる方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="5d38f-342">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="5d38f-343">Act ステップで`GetMessagesAsync` メソッドを実行すると、ページモデルのメソッドが`OnGetAsync`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-343">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="5d38f-344">単体テストの Act ステップ (*tests/RazorPagesTestSample/unittests/IndexPageTests .cs*):</span><span class="sxs-lookup"><span data-stu-id="5d38f-344">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="5d38f-345">`IndexPage`ページモデルの`OnGetAsync`メソッド (*src/RazorPagesTestSample/Pages/Index. cshtml. .cs*):</span><span class="sxs-lookup"><span data-stu-id="5d38f-345">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="5d38f-346">DAL `GetMessagesAsync`のメソッドは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="5d38f-346">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="5d38f-347">メソッドのモックバージョンは、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-347">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="5d38f-348">この手順では、ページモデルの`actualMessages` `Messages`プロパティから実際のメッセージ () が割り当てられます。 `Assert`</span><span class="sxs-lookup"><span data-stu-id="5d38f-348">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="5d38f-349">型チェックは、メッセージが割り当てられたときにも実行されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-349">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="5d38f-350">予想されるメッセージと実際のメッセージは`Text` 、プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-350">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="5d38f-351">テストでは、2つ`List<Message>`のインスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="5d38f-351">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="5d38f-352">このグループの他のテストでは、、、および<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>を確立<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `PageContext`するためのを含むページモデルオブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-352">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="5d38f-353">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="5d38f-353">These are useful in conducting tests.</span></span> <span data-ttu-id="5d38f-354">たとえば、メッセージアプリは、の実行`ModelState`時`OnPostAddMessageAsync`に<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>有効<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>なが返されることを確認するために、でエラーを設定します。</span><span class="sxs-lookup"><span data-stu-id="5d38f-354">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="5d38f-355">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5d38f-355">Additional resources</span></span>

* [<span data-ttu-id="5d38f-356">Dotnet テストC#と xunit を使用した .net Core での単体テスト</span><span class="sxs-lookup"><span data-stu-id="5d38f-356">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="5d38f-357">[コードの単体テスト](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="5d38f-357">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="5d38f-358">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="5d38f-358">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="5d38f-359">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="5d38f-359">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="5d38f-360">XUnit.net の概要:.Net Core と .NET SDK コマンドラインの使用</span><span class="sxs-lookup"><span data-stu-id="5d38f-360">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="5d38f-361">Moq</span><span class="sxs-lookup"><span data-stu-id="5d38f-361">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="5d38f-362">Moq クイックスタート</span><span class="sxs-lookup"><span data-stu-id="5d38f-362">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
