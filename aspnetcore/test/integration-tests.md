---
title: ASP.NET Core での統合テスト
author: guardrex
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: test/integration-tests
ms.openlocfilehash: 195acd3e03f3de63ebd61767f2c86d1c0f38fca5
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017433"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="36d96-103">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="36d96-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="36d96-104">[Luke Latham](https://github.com/guardrex)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="36d96-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="36d96-105">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="36d96-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="36d96-106">ASP.NET Core では、単体テストフレームワークとテスト web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="36d96-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="36d96-107">このトピックでは、単体テストの基本的な知識を前提とします。</span><span class="sxs-lookup"><span data-stu-id="36d96-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="36d96-108">テストの概念にあまり馴染みがない場合、[.NET Core と .NET Standard の単体テスト](/dotnet/core/testing/) のトピックとそこでリンクされているコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="36d96-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="36d96-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="36d96-110">サンプル アプリは、Razor ページ アプリで Razor ページの基本的な知識を前提としています。</span><span class="sxs-lookup"><span data-stu-id="36d96-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="36d96-111">Razor ページに不慣れな場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="36d96-112">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="36d96-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="36d96-113">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="36d96-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="36d96-114">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="36d96-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="36d96-115">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="36d96-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="36d96-116">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="36d96-116">Introduction to integration tests</span></span>

<span data-ttu-id="36d96-117">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="36d96-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="36d96-118">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="36d96-119">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="36d96-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="36d96-120">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="36d96-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="36d96-121">データベース</span><span class="sxs-lookup"><span data-stu-id="36d96-121">Database</span></span>
* <span data-ttu-id="36d96-122">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="36d96-122">File system</span></span>
* <span data-ttu-id="36d96-123">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="36d96-123">Network appliances</span></span>
* <span data-ttu-id="36d96-124">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="36d96-124">Request-response pipeline</span></span>

<span data-ttu-id="36d96-125">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="36d96-126">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="36d96-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="36d96-127">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="36d96-128">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="36d96-128">Require more code and data processing.</span></span>
* <span data-ttu-id="36d96-129">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="36d96-129">Take longer to run.</span></span>

<span data-ttu-id="36d96-130">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="36d96-131">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="36d96-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="36d96-132">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="36d96-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="36d96-133">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="36d96-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="36d96-134">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="36d96-135">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="36d96-136">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="36d96-136">In discussions of integration tests, the tested project is frequently called the *system under test*, or "SUT" for short.</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="36d96-137">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="36d96-137">ASP.NET Core integration tests</span></span>

<span data-ttu-id="36d96-138">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="36d96-138">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="36d96-139">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-139">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="36d96-140">テストプロジェクトには、テスト*対象のシステム*(SUT) と呼ばれるテストされた ASP.NET Core プロジェクトへの参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="36d96-140">The test project has a reference to the tested ASP.NET Core project, called the *system under test* (SUT).</span></span> <span data-ttu-id="36d96-141">_"SUT" は、このトピック全体で、テスト対象のアプリを参照するために使用されます。_</span><span class="sxs-lookup"><span data-stu-id="36d96-141">_"SUT" is used throughout this topic to refer to the tested app._</span></span>
* <span data-ttu-id="36d96-142">テストプロジェクトでは、SUT のテスト web ホストを作成し、テストサーバークライアントを使用して、SUT に対する要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="36d96-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses to the SUT.</span></span>
* <span data-ttu-id="36d96-143">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="36d96-144">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="36d96-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="36d96-145">SUT の web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="36d96-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="36d96-146">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="36d96-147">テストの*配置*ステップが実行されます。テストアプリで要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="36d96-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="36d96-148">*Act*テストステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="36d96-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="36d96-149">*アサート*テストステップが実行されます。*実際*の応答は、*予期される*応答に基づいて、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="36d96-150">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="36d96-151">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-151">The test results are reported.</span></span>

<span data-ttu-id="36d96-152">通常、テスト web ホストは、テストの実行のためにアプリの通常の web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="36d96-153">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="36d96-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="36d96-154">テスト web ホストやメモリ内テストサーバー ([testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="36d96-155">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="36d96-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="36d96-156">パッケージ`Microsoft.AspNetCore.Mvc.Testing`は、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="36d96-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="36d96-157">依存関係ファイル ( *\*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="36d96-157">Copies the dependencies file (*\*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="36d96-158">テストの実行時に静的ファイルとページ/ビューが検出されるように、コンテンツルートを SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="36d96-158">Sets the content root to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="36d96-159">に`TestServer`は、SUT のブートストラップを効率化する[webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="36d96-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="36d96-160">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="36d96-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="36d96-161">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="36d96-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="36d96-162">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="36d96-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="36d96-163">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="36d96-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="36d96-164">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="36d96-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="36d96-165">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="36d96-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="36d96-166">Razor Pages アプリでは、ページエンドポイントのテストは通常、ページモデルクラスの後に名前が`IndexPageTests`付けられます (たとえば、[インデックス] ページのコンポーネントの統合をテストする場合)。</span><span class="sxs-lookup"><span data-stu-id="36d96-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="36d96-167">MVC アプリでは、テストは通常、コントローラークラスによって編成され、テストするコントローラーの後`HomeControllerTests`に名前が付けられます (たとえば、Home コントローラーのコンポーネント統合をテストする場合)。</span><span class="sxs-lookup"><span data-stu-id="36d96-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="36d96-168">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="36d96-168">Test app prerequisites</span></span>

<span data-ttu-id="36d96-169">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="36d96-169">The test project must:</span></span>

* <span data-ttu-id="36d96-170">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="36d96-170">Reference the following packages:</span></span>
  * [<span data-ttu-id="36d96-171">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="36d96-171">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="36d96-172">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="36d96-172">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="36d96-173">プロジェクトファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="36d96-173">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="36d96-174">[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照するときは、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="36d96-174">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="36d96-175">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="36d96-175">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="36d96-176">*テスト/RazorPagesProject/RazorPagesProject*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="36d96-176">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="36d96-177">サンプルアプリでは、 [Xunit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="36d96-177">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="36d96-178">xunit</span><span class="sxs-lookup"><span data-stu-id="36d96-178">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="36d96-179">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="36d96-179">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="36d96-180">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="36d96-180">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="36d96-181">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="36d96-181">SUT environment</span></span>

<span data-ttu-id="36d96-182">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="36d96-182">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="36d96-183">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="36d96-183">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="36d96-184">[Webapplicationfactory&lt;の&gt;](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)このサーバーを使用して、統合テスト用の[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成します。</span><span class="sxs-lookup"><span data-stu-id="36d96-184">[WebApplicationFactory&lt;TEntryPoint&gt;](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="36d96-185">`TEntryPoint`は、SUT のエントリポイントクラス (通常`Startup`はクラス) です。</span><span class="sxs-lookup"><span data-stu-id="36d96-185">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="36d96-186">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="36d96-186">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="36d96-187">アプリエンドポイントの基本テスト</span><span class="sxs-lookup"><span data-stu-id="36d96-187">Basic test of app endpoints</span></span>

<span data-ttu-id="36d96-188">次のテスト クラスでは、`BasicTests`、`WebApplicationFactory` を使用して、SUT をブートストラップし、テストメソッドに [httpclient](/dotnet/api/system.net.http.httpclient) を提供します、`Get_EndpointsReturnSuccessAndCorrectContentType`。</span><span class="sxs-lookup"><span data-stu-id="36d96-188">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="36d96-189">メソッドは、応答の状態コードが成功したかどうか (200-299 の範囲の状態`Content-Type`コード) `text/html; charset=utf-8`を確認し、ヘッダーは複数のアプリページ用です。</span><span class="sxs-lookup"><span data-stu-id="36d96-189">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="36d96-190">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)は、リダイレクトを`HttpClient`自動的に追跡し、cookie を処理するのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="36d96-190">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="36d96-191">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="36d96-191">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="36d96-192">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="36d96-192">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="36d96-193">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-193">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="36d96-194">セキュリティで保護されたエンドポイントをテストする</span><span class="sxs-lookup"><span data-stu-id="36d96-194">Test a secure endpoint</span></span>

<span data-ttu-id="36d96-195">`BasicTests`クラスの別のテストでは、セキュリティで保護されたエンドポイントが、認証されていないユーザーをアプリのログインページにリダイレクトすることを確認します。</span><span class="sxs-lookup"><span data-stu-id="36d96-195">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="36d96-196">SUT では、ページ`/SecurePage`は[authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[authorizepage](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-196">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="36d96-197">詳細については、「 [Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-197">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="36d96-198">テストでは、 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)は[allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を次のように`false`設定することによってリダイレクトを許可しないように設定されています。 `Get_SecurePageRequiresAnAuthenticatedUser`</span><span class="sxs-lookup"><span data-stu-id="36d96-198">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="36d96-199">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="36d96-199">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="36d96-200">SUT によって返される状態コードは、予期された[httpstatuscode. リダイレクト](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [httpstatuscode. OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="36d96-200">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="36d96-201">応答ヘッダーの`http://localhost/Identity/Account/Login` `Location`ヘッダー値は、最後のログインページ応答ではなく、ヘッダーが存在しないことを確認するためにチェックされます。 `Location`</span><span class="sxs-lookup"><span data-stu-id="36d96-201">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="36d96-202">の詳細につい`WebApplicationFactoryClientOptions`ては、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-202">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="36d96-203">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="36d96-203">Customize WebApplicationFactory</span></span>

<span data-ttu-id="36d96-204">Web ホストの構成は、から継承して1つ以上の`WebApplicationFactory`カスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="36d96-204">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="36d96-205">[ConfigureWebHost を](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)継承し、オーバーライドし`WebApplicationFactory`ます。</span><span class="sxs-lookup"><span data-stu-id="36d96-205">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="36d96-206">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="36d96-206">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="36d96-207">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、 `InitializeDbForTests`メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-207">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="36d96-208">このメソッドについては[、統合テストのサンプルを参照してください。テストアプリの](#test-app-organization)組織セクション。</span><span class="sxs-lookup"><span data-stu-id="36d96-208">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="36d96-209">テストクラスで`CustomWebApplicationFactory`カスタムを使用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-209">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="36d96-210">次の例で`IndexPageTests`は、クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-210">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="36d96-211">サンプルアプリのクライアントは、 `HttpClient`次のリダイレクトが行われないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="36d96-211">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="36d96-212">[セキュリティで保護されたエンドポイントのテスト](#test-a-secure-endpoint)に関するセクションで説明したように、これによって、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-212">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="36d96-213">最初の応答は、これらのテストの多くで、 `Location`ヘッダーを使用したリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="36d96-213">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="36d96-214">一般的なテストでは`HttpClient` 、およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="36d96-214">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="36d96-215">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="36d96-215">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="36d96-216">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="36d96-216">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="36d96-217">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="36d96-217">Make a request for the page.</span></span>
1. <span data-ttu-id="36d96-218">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="36d96-218">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="36d96-219">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="36d96-219">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="36d96-220">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) および `GetDocumentAsync` ヘルパーメソッド (*Helpers/HtmlHelpers.cs*) では、[AngleSharp](https://anglesharp.github.io/) パーサーを使用してアンチ偽造を処理します。次のメソッドを確認します。</span><span class="sxs-lookup"><span data-stu-id="36d96-220">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="36d96-221">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="36d96-221">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="36d96-222">`GetDocumentAsync`元`HttpResponseMessage`のに基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-222">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="36d96-223">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-223">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="36d96-224">`SendAsync`の拡張メソッドは`HttpClient` 、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [HttpRequestMessage](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="36d96-224">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="36d96-225">の`SendAsync`オーバーロードは、HTML 形式 (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="36d96-225">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="36d96-226">フォームの [送信] ボタン`IHtmlElement`()</span><span class="sxs-lookup"><span data-stu-id="36d96-226">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="36d96-227">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="36d96-227">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="36d96-228">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="36d96-228">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="36d96-229">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="36d96-229">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="36d96-230">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="36d96-230">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="36d96-231">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="36d96-231">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="36d96-232">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="36d96-232">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="36d96-233">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="36d96-233">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="36d96-234">テストメソッド内で追加の構成が必要な場合、 [withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は`WebApplicationFactory` 、構成によってさらにカスタマイズされた[iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を使用して新しいを作成します。</span><span class="sxs-lookup"><span data-stu-id="36d96-234">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="36d96-235">`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)のテストメソッドは、の`WithWebHostBuilder`使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="36d96-235">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="36d96-236">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="36d96-236">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="36d96-237">`IndexPageTests`クラス内の別のテストによって、データベース内のすべてのレコードを削除し、メソッドの`Post_DeleteMessageHandler_ReturnsRedirectToRoot`前に実行できる操作が実行されるため、このテストメソッドでは、SUT が削除するレコードが存在することを確認するためにデータベースがシード処理されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-237">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is seeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="36d96-238">Sut で`messages`フォームのボタンを選択すると、sutに対する要求でシミュレートされます。`deleteBtn1`</span><span class="sxs-lookup"><span data-stu-id="36d96-238">Selecting the `deleteBtn1` button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="36d96-239">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="36d96-239">Client options</span></span>

<span data-ttu-id="36d96-240">次の表は、`HttpClient` インスタンスの作成時に使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示しています。</span><span class="sxs-lookup"><span data-stu-id="36d96-240">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="36d96-241">オプション</span><span class="sxs-lookup"><span data-stu-id="36d96-241">Option</span></span> | <span data-ttu-id="36d96-242">説明</span><span class="sxs-lookup"><span data-stu-id="36d96-242">Description</span></span> | <span data-ttu-id="36d96-243">既定値</span><span class="sxs-lookup"><span data-stu-id="36d96-243">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="36d96-244">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="36d96-244">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="36d96-245">`HttpClient`インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="36d96-245">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="36d96-246">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="36d96-246">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="36d96-247">インスタンスの`HttpClient`ベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="36d96-247">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="36d96-248">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="36d96-248">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="36d96-249">インスタンスが cookie を`HttpClient`処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="36d96-249">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="36d96-250">Max自動リダイレクト</span><span class="sxs-lookup"><span data-stu-id="36d96-250">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="36d96-251">インスタンスが従う必要がある`HttpClient`リダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="36d96-251">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="36d96-252">7</span><span class="sxs-lookup"><span data-stu-id="36d96-252">7</span></span> |

<span data-ttu-id="36d96-253">`WebApplicationFactoryClientOptions` クラスを作成し、[createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="36d96-253">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="36d96-254">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="36d96-254">Inject mock services</span></span>

<span data-ttu-id="36d96-255">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="36d96-255">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="36d96-256">**モックサービスを挿入するには、SUT が`Startup` `Startup.ConfigureServices`メソッドを持つクラスを持っている必要があります。**</span><span class="sxs-lookup"><span data-stu-id="36d96-256">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="36d96-257">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="36d96-257">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="36d96-258">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="36d96-258">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="36d96-259">*サービス/IQuoteService .cs*:</span><span class="sxs-lookup"><span data-stu-id="36d96-259">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="36d96-260">*サービス/QuoteService (cs*):</span><span class="sxs-lookup"><span data-stu-id="36d96-260">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="36d96-261">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="36d96-261">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="36d96-262">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36d96-262">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="36d96-263">*Pages/Index. .cs*:</span><span class="sxs-lookup"><span data-stu-id="36d96-263">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="36d96-264">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-264">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="36d96-265">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-265">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="36d96-266">モックサービスは、アプリのを`QuoteService`テストアプリによって提供されるサービスに`TestQuoteService`置き換えます。これは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="36d96-266">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="36d96-267">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="36d96-267">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="36d96-268">`ConfigureTestServices`が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-268">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="36d96-269">テストの実行中に生成されたマークアップは、に`TestQuoteService`よって指定された見積もりテキストを反映するため、アサーションは次のように合格します。</span><span class="sxs-lookup"><span data-stu-id="36d96-269">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="36d96-270">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="36d96-270">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="36d96-271">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリ `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリの [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリのコンテンツルートパスを推論します。</span><span class="sxs-lookup"><span data-stu-id="36d96-271">The `WebApplicationFactory` constructor infers the app content root path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="36d96-272">正しいキーを持つ属性が見つからない場合は、 `WebApplicationFactory`フォールバックしてソリューションファイル ( *\*.sln*) を検索し、 `TEntryPoint`アセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="36d96-272">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*\*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="36d96-273">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-273">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="36d96-274">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="36d96-274">Disable shadow copying</span></span>

<span data-ttu-id="36d96-275">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-275">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="36d96-276">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="36d96-276">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="36d96-277">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xunit を使用し、正しい構成設定を含む xunit *. json*ファイルを含めることによって xunit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="36d96-277">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="36d96-278">詳細については、「 [JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-278">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="36d96-279">次のコンテンツを使用して、テストプロジェクトのルートに*xunit のランナー*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="36d96-279">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="36d96-280">Visual Studio を使用している場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピー]** する に設定します。</span><span class="sxs-lookup"><span data-stu-id="36d96-280">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="36d96-281">Visual Studio を使用していない`Content`場合は、テストアプリのプロジェクトファイルにターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="36d96-281">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="36d96-282">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="36d96-282">Disposal of objects</span></span>

<span data-ttu-id="36d96-283">`IClassFixture`実装のテストが実行された後、xunit が[webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[httpclient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-283">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="36d96-284">開発者によってインスタンス化されたオブジェクトが破棄を`IClassFixture`必要とする場合は、実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="36d96-284">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="36d96-285">詳細については、「 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-285">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="36d96-286">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="36d96-286">Integration tests sample</span></span>

<span data-ttu-id="36d96-287">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="36d96-287">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="36d96-288">アプリ</span><span class="sxs-lookup"><span data-stu-id="36d96-288">App</span></span> | <span data-ttu-id="36d96-289">プロジェクトディレクトリ</span><span class="sxs-lookup"><span data-stu-id="36d96-289">Project directory</span></span> | <span data-ttu-id="36d96-290">説明</span><span class="sxs-lookup"><span data-stu-id="36d96-290">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="36d96-291">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="36d96-291">Message app (the SUT)</span></span> | <span data-ttu-id="36d96-292">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="36d96-292">*src/RazorPagesProject*</span></span> | <span data-ttu-id="36d96-293">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="36d96-293">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="36d96-294">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="36d96-294">Test app</span></span> | <span data-ttu-id="36d96-295">*テスト/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="36d96-295">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="36d96-296">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-296">Used to integration test the SUT.</span></span> |

<span data-ttu-id="36d96-297">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="36d96-297">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="36d96-298">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="36d96-298">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="36d96-299">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="36d96-299">Message app (SUT) organization</span></span>

<span data-ttu-id="36d96-300">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="36d96-300">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="36d96-301">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="36d96-301">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="36d96-302">メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-302">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="36d96-303">`Text`プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="36d96-303">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="36d96-304">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-304">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="36d96-305">アプリには、データベースコンテキストクラス`AppDbContext` (*data/appdbcontext .cs*) にデータアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="36d96-305">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="36d96-306">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-306">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="36d96-307">アプリには、 `/SecurePage`認証されたユーザーのみがアクセスできるが含まれています。</span><span class="sxs-lookup"><span data-stu-id="36d96-307">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="36d96-308">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="36d96-308">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="36d96-309">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="36d96-309">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="36d96-310">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="36d96-310">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="36d96-311">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="36d96-311">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="36d96-312">詳細については、「[インフラストラクチャの永続化層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テストコントローラーのロジック](/aspnet/core/mvc/controllers/testing)の設計 (例では、リポジトリパターンを実装します)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36d96-312">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="36d96-313">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="36d96-313">Test app organization</span></span>

<span data-ttu-id="36d96-314">テストアプリは、 *tests/RazorPagesProject*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="36d96-314">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="36d96-315">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="36d96-315">Test app directory</span></span> | <span data-ttu-id="36d96-316">説明</span><span class="sxs-lookup"><span data-stu-id="36d96-316">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="36d96-317">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="36d96-317">*BasicTests*</span></span> | <span data-ttu-id="36d96-318">*BasicTests.cs*には、ルーティング用のテストメソッド、認証されていないユーザーによるセキュリティで保護されたページへのアクセス、GitHub ユーザープロファイルの取得、およびプロファイルのユーザーログインの確認が含まれています。</span><span class="sxs-lookup"><span data-stu-id="36d96-318">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="36d96-319">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="36d96-319">*IntegrationTests*</span></span> | <span data-ttu-id="36d96-320">*IndexPageTests.cs*には、カスタム`WebApplicationFactory`クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="36d96-320">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="36d96-321">*ヘルパー/ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="36d96-321">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="36d96-322">*Utilities.cs*には`InitializeDbForTests` 、テストデータを使用したデータベースのシード処理に使用するメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="36d96-322">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="36d96-323">*HtmlHelpers.cs*は、テストメソッドによって`IHtmlDocument`使用される AngleSharp を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="36d96-323">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="36d96-324">*HttpClientExtensions.cs*は、SUT `SendAsync`に要求を送信するためのオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="36d96-324">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="36d96-325">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="36d96-325">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="36d96-326">統合テストは、 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="36d96-326">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="36d96-327">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、パッケージと`TestHost` `TestServer`パッケージはテストアプリのプロジェクトファイルまたは開発者に直接パッケージ参照を必要としません。テストアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="36d96-327">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="36d96-328">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="36d96-328">**Seeding the database for testing**</span></span>

<span data-ttu-id="36d96-329">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="36d96-329">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="36d96-330">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="36d96-330">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="36d96-331">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="36d96-331">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a><span data-ttu-id="36d96-332">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="36d96-332">Additional resources</span></span>

* [<span data-ttu-id="36d96-333">単体テスト</span><span class="sxs-lookup"><span data-stu-id="36d96-333">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="36d96-334">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="36d96-334">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="36d96-335">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="36d96-335">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="36d96-336">テスト コントローラー</span><span class="sxs-lookup"><span data-stu-id="36d96-336">Test controllers</span></span>](xref:mvc/controllers/testing)
