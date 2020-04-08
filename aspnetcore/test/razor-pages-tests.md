---
title: ASP.NET Core での Razor Pages の単体テスト
author: rick-anderson
description: Razor Pages アプリの単体テストを作成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649574"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="65ed7-103">ASP.NET Core での Razor Pages の単体テスト</span><span class="sxs-lookup"><span data-stu-id="65ed7-103">Razor Pages unit tests in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65ed7-104">ASP.NET Core では Razor Pages アプリの単体テストをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-104">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="65ed7-105">データ アクセス層 (DAL) とページ モデルのテストによって、次のことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-105">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="65ed7-106">Razor Pages アプリの一部は、アプリの構築時に独立して、または 1 つの単位として連携して機能します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-106">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="65ed7-107">クラスとメソッドの担当のスコープは限定されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-107">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="65ed7-108">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-108">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="65ed7-109">コードの更新によって発生したエラーである回帰は、自動ビルドおよびデプロイ時に検出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-109">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="65ed7-110">このトピックでは、Razor Pages アプリと単体テストの基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-110">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="65ed7-111">Razor Pages アプリまたはテストの概念になじみがない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed7-111">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="65ed7-112">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="65ed7-112">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="65ed7-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65ed7-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="65ed7-114">サンプル プロジェクトは、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-114">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="65ed7-115">アプリ</span><span class="sxs-lookup"><span data-stu-id="65ed7-115">App</span></span>         | <span data-ttu-id="65ed7-116">プロジェクト フォルダー</span><span class="sxs-lookup"><span data-stu-id="65ed7-116">Project folder</span></span>                     | <span data-ttu-id="65ed7-117">説明</span><span class="sxs-lookup"><span data-stu-id="65ed7-117">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="65ed7-118">メッセージ アプリ</span><span class="sxs-lookup"><span data-stu-id="65ed7-118">Message app</span></span> | <span data-ttu-id="65ed7-119">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="65ed7-119">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="65ed7-120">ユーザーがメッセージの追加、1 つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析 (メッセージあたりの平均単語数の検出) をできるようにします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-120">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="65ed7-121">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="65ed7-121">Test app</span></span>    | <span data-ttu-id="65ed7-122">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="65ed7-122">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="65ed7-123">メッセージ アプリの DAL およびインデックス ページ モデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-123">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="65ed7-124">IDE の組み込みテスト機能 ([Visual Studio](/visualstudio/test/unit-test-your-code) や [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) など) を使用して、テストを実行できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-124">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="65ed7-125">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用する場合は、コマンドプロンプトで *tests/RazorPagesTestSample* フォルダー内の次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-125">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="65ed7-126">メッセージ アプリの構成</span><span class="sxs-lookup"><span data-stu-id="65ed7-126">Message app organization</span></span>

<span data-ttu-id="65ed7-127">メッセージ アプリは、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-127">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="65ed7-128">アプリのインデックス ページ (*Pages/Index. cshtml* と *Pages/Index. cshtml*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数の検出) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-128">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="65ed7-129">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-129">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="65ed7-130">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-130">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="65ed7-131">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-131">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="65ed7-132">アプリでは、データベース コンテキスト クラス `AppDbContext` (*Data/AppDbContext.cs*) 内に DAL を格納します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-132">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="65ed7-133">DAL メソッドは `virtual` とマークされ、これにより、テストで使用するためのメソッドのモックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-133">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="65ed7-134">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-134">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="65ed7-135">これらの*シードされたメッセージ*も、テストで使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-135">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="65ed7-136">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-136">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="65ed7-137">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-137">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="65ed7-138">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="65ed7-138">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="65ed7-139">サンプル アプリではリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-139">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="65ed7-140">詳細については、[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)に関するページと「<xref:mvc/controllers/testing>」を参照してください (このサンプルでは、リポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="65ed7-140">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="65ed7-141">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="65ed7-141">Test app organization</span></span>

<span data-ttu-id="65ed7-142">テスト アプリは、*tests/RazorPagesTestSample.Tests* フォルダー内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-142">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="65ed7-143">テスト アプリ フォルダー</span><span class="sxs-lookup"><span data-stu-id="65ed7-143">Test app folder</span></span> | <span data-ttu-id="65ed7-144">説明</span><span class="sxs-lookup"><span data-stu-id="65ed7-144">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="65ed7-145">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="65ed7-145">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="65ed7-146">*DataAccessLayerTest.cs* には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-146">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="65ed7-147">*IndexPageTests.cs* には、インデックス ページ モデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-147">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="65ed7-148">*Utilities*</span><span class="sxs-lookup"><span data-stu-id="65ed7-148">*Utilities*</span></span>     | <span data-ttu-id="65ed7-149">データベースが各テストのベースライン条件にリセットされるように、各 DAL 単体テストの新しいデータベース コンテキスト オプションを作成するために使用する `TestDbContextOptions` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-149">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="65ed7-150">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="65ed7-150">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="65ed7-151">オブジェクトのモック フレームワークは [Moq](https://github.com/moq/moq4) です。</span><span class="sxs-lookup"><span data-stu-id="65ed7-151">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="65ed7-152">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="65ed7-152">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="65ed7-153">メッセージ アプリには、`AppDbContext` クラス (*src/RazorPagesTestSample/Data/AppDbContext.cs*) に含まれる 4 つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-153">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="65ed7-154">テスト アプリで、各メソッドには 1 つか 2 つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-154">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="65ed7-155">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="65ed7-155">DAL method</span></span>               | <span data-ttu-id="65ed7-156">関数</span><span class="sxs-lookup"><span data-stu-id="65ed7-156">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="65ed7-157">データベースから `Text` プロパティで並べ替えられた `List<Message>` を取得します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-157">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="65ed7-158">`Message` をデータベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-158">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="65ed7-159">データベースからすべての `Message` エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-159">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="65ed7-160">`Id` によって、データベースから 1 つの `Message` を削除します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-160">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="65ed7-161">DAL の単体テストでは、各テストの新しい `AppDbContext` を作成する際に、<xref:Microsoft.EntityFrameworkCore.DbContextOptions> が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed7-161">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="65ed7-162">各テストの `DbContextOptions` を作成する 1 つの方法として、<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-162">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="65ed7-163">この方法の問題は、各テストでは、前のテストでデータベースがどのような状態になっていても、それを受け取るということです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-163">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="65ed7-164">相互に干渉しないアトミック単体テストを作成しようとする場合に、これが問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-164">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="65ed7-165">`AppDbContext` でテストごとに新しいデータベース コンテキストを強制的に使用させるには、新しいサービス プロバイダーに基づいた `DbContextOptions` インスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-165">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="65ed7-166">テスト アプリでは、その `Utilities` クラスメソッド `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*) を使用してこれを行う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-166">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="65ed7-167">DAL 単体テストで `DbContextOptions` を使用すると、新しいデータベース インスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-167">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="65ed7-168">`DataAccessLayerTest` クラスの各テスト メソッド (*UnitTests/DataAccessLayerTest.cs*) は、同様の Arrange-Act-Assert パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="65ed7-168">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="65ed7-169">Arrange (環境構築):データベースがテスト用に構成され、期待される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-169">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="65ed7-170">Act (実行):テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-170">Act: The test is executed.</span></span>
1. <span data-ttu-id="65ed7-171">Assert (動作確認):アサーションによって、テスト結果が成功であるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-171">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="65ed7-172">たとえば、`DeleteMessageAsync` メソッドは、`Id` によって識別された 1 つのメッセージの削除を担当します (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="65ed7-172">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="65ed7-173">このメソッドには 2 つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-173">There are two tests for this method.</span></span> <span data-ttu-id="65ed7-174">1 つのテストでは、メッセージがデータベースに存在する場合に、このメソッドによってメッセージが削除されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-174">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="65ed7-175">他方のメソッドでは、削除対象のメッセージ `Id` が存在しない場合に、データベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-175">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="65ed7-176">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` メソッドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-176">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="65ed7-177">まず、このメソッドでは、Arrange ステップを実行し、Act ステップの準備を行います。</span><span class="sxs-lookup"><span data-stu-id="65ed7-177">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="65ed7-178">シード処理メッセージが取得され、`seedMessages` で保持されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-178">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="65ed7-179">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-179">The seeding messages are saved into the database.</span></span> <span data-ttu-id="65ed7-180">`Id` が `1` のメッセージは、削除対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-180">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="65ed7-181">`DeleteMessageAsync` メソッドが実行されると、予期されるメッセージには、`Id` が `1` のメッセージを除くすべてのメッセージが含まれるはずです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-181">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="65ed7-182">`expectedMessages` 変数は、この予期される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-182">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="65ed7-183">メソッドは次のように動作します。`DeleteMessageAsync` メソッドは `1`の `recId` を渡して実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-183">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="65ed7-184">最後に、メソッドはコンテキストから `Messages` を取得し、`expectedMessages` と比較して、2 つが等しいことをアサートします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-184">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="65ed7-185">2 つの `List<Message>` が同じであることを比較するために:</span><span class="sxs-lookup"><span data-stu-id="65ed7-185">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="65ed7-186">メッセージは `Id` 順に並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-186">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="65ed7-187">メッセージのペアは、`Text` プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-187">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="65ed7-188">類似のテスト メソッド `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` では、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-188">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="65ed7-189">この場合、データベース内の予期されるメッセージは、`DeleteMessageAsync` メソッドが実行された後の実際のメッセージと等しくなるはずです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-189">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="65ed7-190">データベースのコンテンツへの変更はないはずです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-190">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="65ed7-191">ページ モデル メソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="65ed7-191">Unit tests of the page model methods</span></span>

<span data-ttu-id="65ed7-192">別の単体テスト セットは、ページ モデル メソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-192">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="65ed7-193">メッセージ アプリで、インデックス ページ モデルは *src/RazorPagesTestSample/Pages/Index.cshtml.cs* の `IndexModel` クラスにあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-193">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="65ed7-194">ページ モデル メソッド</span><span class="sxs-lookup"><span data-stu-id="65ed7-194">Page model method</span></span> | <span data-ttu-id="65ed7-195">関数</span><span class="sxs-lookup"><span data-stu-id="65ed7-195">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="65ed7-196">`GetMessagesAsync` メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-196">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="65ed7-197">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が有効な場合、`AddMessageAsync` を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-197">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="65ed7-198">データベース内のすべてのメッセージを削除するには、`DeleteAllMessagesAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-198">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="65ed7-199">`Id` を指定してメッセージを削除するには、`DeleteMessageAsync` を実行します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-199">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="65ed7-200">データベースに 1 つ以上のメッセージが含まれている場合、メッセージあたりの平均単語数を計算します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-200">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="65ed7-201">ページ モデル メソッドは、`IndexPageTests` クラス (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) の 7 つのテストを使用してテストされます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-201">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="65ed7-202">テストでは、おなじみの Arrange-Assert-Act パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-202">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="65ed7-203">これらのテストでは次に焦点を合わせています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-203">These tests focus on:</span></span>

* <span data-ttu-id="65ed7-204">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-204">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="65ed7-205">メソッドによって正しい <xref:Microsoft.AspNetCore.Mvc.IActionResult> が生成されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-205">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="65ed7-206">プロパティ値の割り当てが正しく行われていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-206">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="65ed7-207">この一連のテストでは、多くの場合に、ページ モデル メソッドが実行される Act ステップ用に予期されるデータを生成するために、DAL のメソッドのモックを作成します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-207">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="65ed7-208">たとえば、`AppDbContext` の `GetMessagesAsync` メソッドは、出力を生成するためにモックが作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-208">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="65ed7-209">ページ モデル メソッドでこのメソッドを実行すると、モックによって結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-209">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="65ed7-210">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="65ed7-210">The data doesn't come from the database.</span></span> <span data-ttu-id="65ed7-211">これにより、ページ モデル テストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-211">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="65ed7-212">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` テストでは、ページ モデルに対して `GetMessagesAsync` メソッドのモックを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-212">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="65ed7-213">Act ステップで `OnGetAsync` メソッドが実行されると、ページ モデルの `GetMessagesAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-213">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="65ed7-214">単体テストの Act ステップ (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="65ed7-214">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="65ed7-215">`IndexPage` ページモデルの `OnGetAsync` メソッド (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="65ed7-215">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="65ed7-216">DAL の `GetMessagesAsync` メソッドでは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="65ed7-216">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="65ed7-217">メソッドのモック バージョンで、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-217">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="65ed7-218">`Assert` ステップでは、実際のメッセージ (`actualMessages`) がページ モデルの `Messages` プロパティから割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-218">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="65ed7-219">メッセージが割り当てられるときに、型チェックも実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-219">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="65ed7-220">予期されるメッセージと実際のメッセージが、それらの `Text` プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-220">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="65ed7-221">テストでは、2 つの `List<Message>` インスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-221">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="65ed7-222">このグループの他のテストでは、<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、`PageContext` を確立するための <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary`、および `PageContext` を含むページ モデル オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-222">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="65ed7-223">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-223">These are useful in conducting tests.</span></span> <span data-ttu-id="65ed7-224">たとえば、メッセージ アプリでは、`OnPostAddMessageAsync` の実行時に有効な <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> が返されることを確認するために、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> を使用して `ModelState` エラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-224">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="65ed7-225">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="65ed7-225">Additional resources</span></span>

* [<span data-ttu-id="65ed7-226">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="65ed7-226">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="65ed7-227">[コードの単体テスト](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="65ed7-227">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="65ed7-228">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="65ed7-228">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="65ed7-229">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="65ed7-229">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="65ed7-230">XUnit.net の概要:.NET SDK コマンド ラインでの .Net Core の使用</span><span class="sxs-lookup"><span data-stu-id="65ed7-230">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="65ed7-231">Moq</span><span class="sxs-lookup"><span data-stu-id="65ed7-231">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="65ed7-232">Moq クイック スタート</span><span class="sxs-lookup"><span data-stu-id="65ed7-232">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65ed7-233">ASP.NET Core では Razor Pages アプリの単体テストをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-233">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="65ed7-234">データ アクセス層 (DAL) とページ モデルのテストによって、次のことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-234">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="65ed7-235">Razor Pages アプリの一部は、アプリの構築時に独立して、または 1 つの単位として連携して機能します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-235">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="65ed7-236">クラスとメソッドの担当のスコープは限定されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-236">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="65ed7-237">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-237">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="65ed7-238">コードの更新によって発生したエラーである回帰は、自動ビルドおよびデプロイ時に検出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-238">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="65ed7-239">このトピックでは、Razor Pages アプリと単体テストの基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-239">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="65ed7-240">Razor Pages アプリまたはテストの概念になじみがない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed7-240">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="65ed7-241">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="65ed7-241">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="65ed7-242">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65ed7-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="65ed7-243">サンプル プロジェクトは、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-243">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="65ed7-244">アプリ</span><span class="sxs-lookup"><span data-stu-id="65ed7-244">App</span></span>         | <span data-ttu-id="65ed7-245">プロジェクト フォルダー</span><span class="sxs-lookup"><span data-stu-id="65ed7-245">Project folder</span></span>                     | <span data-ttu-id="65ed7-246">説明</span><span class="sxs-lookup"><span data-stu-id="65ed7-246">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="65ed7-247">メッセージ アプリ</span><span class="sxs-lookup"><span data-stu-id="65ed7-247">Message app</span></span> | <span data-ttu-id="65ed7-248">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="65ed7-248">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="65ed7-249">ユーザーがメッセージの追加、1 つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析 (メッセージあたりの平均単語数の検出) をできるようにします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-249">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="65ed7-250">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="65ed7-250">Test app</span></span>    | <span data-ttu-id="65ed7-251">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="65ed7-251">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="65ed7-252">メッセージ アプリの DAL およびインデックス ページ モデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-252">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="65ed7-253">IDE の組み込みテスト機能 ([Visual Studio](/visualstudio/test/unit-test-your-code) や [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) など) を使用して、テストを実行できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-253">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="65ed7-254">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用する場合は、コマンドプロンプトで *tests/RazorPagesTestSample* フォルダー内の次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-254">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="65ed7-255">メッセージ アプリの構成</span><span class="sxs-lookup"><span data-stu-id="65ed7-255">Message app organization</span></span>

<span data-ttu-id="65ed7-256">メッセージ アプリは、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-256">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="65ed7-257">アプリのインデックス ページ (*Pages/Index. cshtml* と *Pages/Index. cshtml*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数の検出) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-257">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="65ed7-258">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-258">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="65ed7-259">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-259">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="65ed7-260">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-260">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="65ed7-261">アプリでは、データベース コンテキスト クラス `AppDbContext` (*Data/AppDbContext.cs*) 内に DAL を格納します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-261">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="65ed7-262">DAL メソッドは `virtual` とマークされ、これにより、テストで使用するためのメソッドのモックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-262">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="65ed7-263">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-263">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="65ed7-264">これらの*シードされたメッセージ*も、テストで使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-264">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="65ed7-265">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-265">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="65ed7-266">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-266">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="65ed7-267">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="65ed7-267">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="65ed7-268">サンプル アプリではリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-268">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="65ed7-269">詳細については、[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)に関するページと「<xref:mvc/controllers/testing>」を参照してください (このサンプルでは、リポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="65ed7-269">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="65ed7-270">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="65ed7-270">Test app organization</span></span>

<span data-ttu-id="65ed7-271">テスト アプリは、*tests/RazorPagesTestSample.Tests* フォルダー内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-271">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="65ed7-272">テスト アプリ フォルダー</span><span class="sxs-lookup"><span data-stu-id="65ed7-272">Test app folder</span></span> | <span data-ttu-id="65ed7-273">説明</span><span class="sxs-lookup"><span data-stu-id="65ed7-273">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="65ed7-274">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="65ed7-274">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="65ed7-275">*DataAccessLayerTest.cs* には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-275">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="65ed7-276">*IndexPageTests.cs* には、インデックス ページ モデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-276">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="65ed7-277">*Utilities*</span><span class="sxs-lookup"><span data-stu-id="65ed7-277">*Utilities*</span></span>     | <span data-ttu-id="65ed7-278">データベースが各テストのベースライン条件にリセットされるように、各 DAL 単体テストの新しいデータベース コンテキスト オプションを作成するために使用する `TestDbContextOptions` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-278">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="65ed7-279">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="65ed7-279">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="65ed7-280">オブジェクトのモック フレームワークは [Moq](https://github.com/moq/moq4) です。</span><span class="sxs-lookup"><span data-stu-id="65ed7-280">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="65ed7-281">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="65ed7-281">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="65ed7-282">メッセージ アプリには、`AppDbContext` クラス (*src/RazorPagesTestSample/Data/AppDbContext.cs*) に含まれる 4 つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-282">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="65ed7-283">テスト アプリで、各メソッドには 1 つか 2 つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-283">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="65ed7-284">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="65ed7-284">DAL method</span></span>               | <span data-ttu-id="65ed7-285">関数</span><span class="sxs-lookup"><span data-stu-id="65ed7-285">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="65ed7-286">データベースから `Text` プロパティで並べ替えられた `List<Message>` を取得します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-286">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="65ed7-287">`Message` をデータベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-287">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="65ed7-288">データベースからすべての `Message` エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-288">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="65ed7-289">`Id` によって、データベースから 1 つの `Message` を削除します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-289">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="65ed7-290">DAL の単体テストでは、各テストの新しい `AppDbContext` を作成する際に、<xref:Microsoft.EntityFrameworkCore.DbContextOptions> が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed7-290">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="65ed7-291">各テストの `DbContextOptions` を作成する 1 つの方法として、<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-291">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="65ed7-292">この方法の問題は、各テストでは、前のテストでデータベースがどのような状態になっていても、それを受け取るということです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-292">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="65ed7-293">相互に干渉しないアトミック単体テストを作成しようとする場合に、これが問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-293">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="65ed7-294">`AppDbContext` でテストごとに新しいデータベース コンテキストを強制的に使用させるには、新しいサービス プロバイダーに基づいた `DbContextOptions` インスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-294">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="65ed7-295">テスト アプリでは、その `Utilities` クラスメソッド `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*) を使用してこれを行う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-295">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="65ed7-296">DAL 単体テストで `DbContextOptions` を使用すると、新しいデータベース インスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-296">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="65ed7-297">`DataAccessLayerTest` クラスの各テスト メソッド (*UnitTests/DataAccessLayerTest.cs*) は、同様の Arrange-Act-Assert パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="65ed7-297">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="65ed7-298">Arrange (環境構築):データベースがテスト用に構成され、期待される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-298">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="65ed7-299">Act (実行):テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-299">Act: The test is executed.</span></span>
1. <span data-ttu-id="65ed7-300">Assert (動作確認):アサーションによって、テスト結果が成功であるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-300">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="65ed7-301">たとえば、`DeleteMessageAsync` メソッドは、`Id` によって識別された 1 つのメッセージの削除を担当します (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="65ed7-301">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="65ed7-302">このメソッドには 2 つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-302">There are two tests for this method.</span></span> <span data-ttu-id="65ed7-303">1 つのテストでは、メッセージがデータベースに存在する場合に、このメソッドによってメッセージが削除されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-303">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="65ed7-304">他方のメソッドでは、削除対象のメッセージ `Id` が存在しない場合に、データベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-304">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="65ed7-305">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` メソッドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-305">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="65ed7-306">まず、このメソッドでは、Arrange ステップを実行し、Act ステップの準備を行います。</span><span class="sxs-lookup"><span data-stu-id="65ed7-306">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="65ed7-307">シード処理メッセージが取得され、`seedMessages` で保持されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-307">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="65ed7-308">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-308">The seeding messages are saved into the database.</span></span> <span data-ttu-id="65ed7-309">`Id` が `1` のメッセージは、削除対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-309">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="65ed7-310">`DeleteMessageAsync` メソッドが実行されると、予期されるメッセージには、`Id` が `1` のメッセージを除くすべてのメッセージが含まれるはずです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-310">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="65ed7-311">`expectedMessages` 変数は、この予期される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-311">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="65ed7-312">メソッドは次のように動作します。`DeleteMessageAsync` メソッドは `1`の `recId` を渡して実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-312">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="65ed7-313">最後に、メソッドはコンテキストから `Messages` を取得し、`expectedMessages` と比較して、2 つが等しいことをアサートします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-313">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="65ed7-314">2 つの `List<Message>` が同じであることを比較するために:</span><span class="sxs-lookup"><span data-stu-id="65ed7-314">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="65ed7-315">メッセージは `Id` 順に並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-315">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="65ed7-316">メッセージのペアは、`Text` プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-316">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="65ed7-317">類似のテスト メソッド `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` では、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-317">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="65ed7-318">この場合、データベース内の予期されるメッセージは、`DeleteMessageAsync` メソッドが実行された後の実際のメッセージと等しくなるはずです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-318">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="65ed7-319">データベースのコンテンツへの変更はないはずです。</span><span class="sxs-lookup"><span data-stu-id="65ed7-319">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="65ed7-320">ページ モデル メソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="65ed7-320">Unit tests of the page model methods</span></span>

<span data-ttu-id="65ed7-321">別の単体テスト セットは、ページ モデル メソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-321">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="65ed7-322">メッセージ アプリで、インデックス ページ モデルは *src/RazorPagesTestSample/Pages/Index.cshtml.cs* の `IndexModel` クラスにあります。</span><span class="sxs-lookup"><span data-stu-id="65ed7-322">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="65ed7-323">ページ モデル メソッド</span><span class="sxs-lookup"><span data-stu-id="65ed7-323">Page model method</span></span> | <span data-ttu-id="65ed7-324">関数</span><span class="sxs-lookup"><span data-stu-id="65ed7-324">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="65ed7-325">`GetMessagesAsync` メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-325">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="65ed7-326">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が有効な場合、`AddMessageAsync` を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-326">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="65ed7-327">データベース内のすべてのメッセージを削除するには、`DeleteAllMessagesAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-327">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="65ed7-328">`Id` を指定してメッセージを削除するには、`DeleteMessageAsync` を実行します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-328">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="65ed7-329">データベースに 1 つ以上のメッセージが含まれている場合、メッセージあたりの平均単語数を計算します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-329">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="65ed7-330">ページ モデル メソッドは、`IndexPageTests` クラス (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) の 7 つのテストを使用してテストされます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-330">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="65ed7-331">テストでは、おなじみの Arrange-Assert-Act パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-331">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="65ed7-332">これらのテストでは次に焦点を合わせています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-332">These tests focus on:</span></span>

* <span data-ttu-id="65ed7-333">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-333">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="65ed7-334">メソッドによって正しい <xref:Microsoft.AspNetCore.Mvc.IActionResult> が生成されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-334">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="65ed7-335">プロパティ値の割り当てが正しく行われていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-335">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="65ed7-336">この一連のテストでは、多くの場合に、ページ モデル メソッドが実行される Act ステップ用に予期されるデータを生成するために、DAL のメソッドのモックを作成します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-336">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="65ed7-337">たとえば、`AppDbContext` の `GetMessagesAsync` メソッドは、出力を生成するためにモックが作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-337">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="65ed7-338">ページ モデル メソッドでこのメソッドを実行すると、モックによって結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-338">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="65ed7-339">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="65ed7-339">The data doesn't come from the database.</span></span> <span data-ttu-id="65ed7-340">これにより、ページ モデル テストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-340">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="65ed7-341">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` テストでは、ページ モデルに対して `GetMessagesAsync` メソッドのモックを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed7-341">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="65ed7-342">Act ステップで `OnGetAsync` メソッドが実行されると、ページ モデルの `GetMessagesAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-342">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="65ed7-343">単体テストの Act ステップ (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="65ed7-343">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="65ed7-344">`IndexPage` ページモデルの `OnGetAsync` メソッド (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="65ed7-344">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="65ed7-345">DAL の `GetMessagesAsync` メソッドでは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="65ed7-345">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="65ed7-346">メソッドのモック バージョンで、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-346">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="65ed7-347">`Assert` ステップでは、実際のメッセージ (`actualMessages`) がページ モデルの `Messages` プロパティから割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-347">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="65ed7-348">メッセージが割り当てられるときに、型チェックも実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-348">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="65ed7-349">予期されるメッセージと実際のメッセージが、それらの `Text` プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-349">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="65ed7-350">テストでは、2 つの `List<Message>` インスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="65ed7-350">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="65ed7-351">このグループの他のテストでは、<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、`PageContext` を確立するための <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary`、および `PageContext` を含むページ モデル オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-351">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="65ed7-352">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65ed7-352">These are useful in conducting tests.</span></span> <span data-ttu-id="65ed7-353">たとえば、メッセージ アプリでは、`OnPostAddMessageAsync` の実行時に有効な <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> が返されることを確認するために、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> を使用して `ModelState` エラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65ed7-353">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="65ed7-354">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="65ed7-354">Additional resources</span></span>

* [<span data-ttu-id="65ed7-355">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="65ed7-355">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="65ed7-356">[コードの単体テスト](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="65ed7-356">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="65ed7-357">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="65ed7-357">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="65ed7-358">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="65ed7-358">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="65ed7-359">XUnit.net の概要:.NET SDK コマンド ラインでの .Net Core の使用</span><span class="sxs-lookup"><span data-stu-id="65ed7-359">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="65ed7-360">Moq</span><span class="sxs-lookup"><span data-stu-id="65ed7-360">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="65ed7-361">Moq クイック スタート</span><span class="sxs-lookup"><span data-stu-id="65ed7-361">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
