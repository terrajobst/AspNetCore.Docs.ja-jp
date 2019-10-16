---
title: ASP.NET Core での統合テスト
author: guardrex
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2019
uid: test/integration-tests
ms.openlocfilehash: 863b95230d376d050c34a9ed585b7696e649cb05
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378713"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="6e92e-103">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="6e92e-104">[Luke Latham](https://github.com/guardrex)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6e92e-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6e92e-105">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="6e92e-106">ASP.NET Core では、単体テストフレームワークとテスト web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="6e92e-107">このトピックでは、単体テストの基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="6e92e-108">テストの概念を理解していない場合は、 [.Net Core の単体テストと .NET Standard に](/dotnet/core/testing/)関するトピックとそのリンク先を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="6e92e-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6e92e-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6e92e-110">このサンプルアプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="6e92e-111">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="6e92e-112">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="6e92e-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="6e92e-113">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="6e92e-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="6e92e-114">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="6e92e-115">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="6e92e-116">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="6e92e-116">Introduction to integration tests</span></span>

<span data-ttu-id="6e92e-117">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="6e92e-118">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="6e92e-119">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="6e92e-120">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="6e92e-121">データベース</span><span class="sxs-lookup"><span data-stu-id="6e92e-121">Database</span></span>
* <span data-ttu-id="6e92e-122">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="6e92e-122">File system</span></span>
* <span data-ttu-id="6e92e-123">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="6e92e-123">Network appliances</span></span>
* <span data-ttu-id="6e92e-124">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="6e92e-124">Request-response pipeline</span></span>

<span data-ttu-id="6e92e-125">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="6e92e-126">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="6e92e-127">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="6e92e-128">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-128">Require more code and data processing.</span></span>
* <span data-ttu-id="6e92e-129">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-129">Take longer to run.</span></span>

<span data-ttu-id="6e92e-130">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="6e92e-131">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="6e92e-132">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="6e92e-133">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="6e92e-134">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="6e92e-135">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="6e92e-136">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="6e92e-137">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="6e92e-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="6e92e-138">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="6e92e-139">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="6e92e-140">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="6e92e-141">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="6e92e-142">テストプロジェクトでは、SUT のテスト web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="6e92e-143">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="6e92e-144">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="6e92e-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="6e92e-145">SUT の web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="6e92e-146">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="6e92e-147">テストの*配置*ステップが実行されます。テストアプリは要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="6e92e-148">*Act*テストステップが実行されます。クライアントが要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="6e92e-149">*アサート*テストステップが実行されます。*実際*の応答は*成功*として検証されるか、*予期される*応答に基づいて*失敗*します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="6e92e-150">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="6e92e-151">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-151">The test results are reported.</span></span>

<span data-ttu-id="6e92e-152">通常、テスト web ホストは、テストの実行のためにアプリの通常の web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="6e92e-153">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="6e92e-154">テスト web ホストやメモリ内テストサーバー ([testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="6e92e-155">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="6e92e-156">@No__t-0 パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="6e92e-157">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="6e92e-158">テストの実行時に静的ファイルとページ/ビューが検出されるように、[コンテンツルート](xref:fundamentals/index#content-root)を SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="6e92e-159">@No__t-1 での SUT のブートストラップ化を効率化する[Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="6e92e-160">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="6e92e-161">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="6e92e-162">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="6e92e-163">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="6e92e-164">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="6e92e-165">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="6e92e-166">Razor Pages アプリでは、通常、ページエンドポイントのテストはページモデルクラスの後に名前が付けられます (たとえば、インデックスページのコンポーネントの統合をテストするには、`IndexPageTests` になります)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="6e92e-167">MVC アプリでは、テストは通常、コントローラークラス別に編成され、テスト対象のコントローラーの後に名前が付けられます (たとえば、Home コントローラーのコンポーネントの統合をテストするには、`HomeControllerTests`)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="6e92e-168">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="6e92e-168">Test app prerequisites</span></span>

<span data-ttu-id="6e92e-169">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-169">The test project must:</span></span>

* <span data-ttu-id="6e92e-170">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="6e92e-171">プロジェクトファイルで Web SDK を指定します (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="6e92e-172">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="6e92e-173">*テスト/RazorPagesProject/RazorPagesProject*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="6e92e-174">サンプルアプリでは、 [Xunit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="6e92e-175">xunit</span><span class="sxs-lookup"><span data-stu-id="6e92e-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="6e92e-176">xunit</span><span class="sxs-lookup"><span data-stu-id="6e92e-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="6e92e-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="6e92e-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="6e92e-178">テストでも Entity Framework Core が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="6e92e-179">アプリの参照:</span><span class="sxs-lookup"><span data-stu-id="6e92e-179">The app references:</span></span>

* [<span data-ttu-id="6e92e-180">AspNetCore コア (Microsoft. 診断)</span><span class="sxs-lookup"><span data-stu-id="6e92e-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="6e92e-181">AspNetCore コア (Microsoft. Identity)</span><span class="sxs-lookup"><span data-stu-id="6e92e-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="6e92e-182">Microsoft EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="6e92e-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="6e92e-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="6e92e-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="6e92e-184">Microsoft EntityFrameworkCore. ツール</span><span class="sxs-lookup"><span data-stu-id="6e92e-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="6e92e-185">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="6e92e-185">SUT environment</span></span>

<span data-ttu-id="6e92e-186">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="6e92e-187">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="6e92e-188">[Webapplicationfactory @ no__t-> 1:](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)統合テスト用の[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="6e92e-189">`TEntryPoint` は、SUT のエントリポイントクラス (通常は `Startup` クラス) です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="6e92e-190">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="6e92e-191">アプリエンドポイントの基本テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-191">Basic test of app endpoints</span></span>

<span data-ttu-id="6e92e-192">次のテストクラス (`BasicTests`) では、`WebApplicationFactory` を使用して、SUT をブートストラップし、テストメソッドに[Httpclient](/dotnet/api/system.net.http.httpclient)を指定します (@no__t 3)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-192">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="6e92e-193">メソッドは、応答ステータスコードが正常に実行されたかどうかを確認します (200-299 の範囲の状態コード)。また、複数のアプリページで @no__t は、`Content-Type` ヘッダーが-1 になります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-193">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="6e92e-194">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)が `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトを追跡し、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="6e92e-195">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-195">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="6e92e-196">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-196">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="6e92e-197">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-197">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="6e92e-198">セキュリティで保護されたエンドポイントをテストする</span><span class="sxs-lookup"><span data-stu-id="6e92e-198">Test a secure endpoint</span></span>

<span data-ttu-id="6e92e-199">@No__t-0 クラスのもう1つのテストは、セキュリティで保護されたエンドポイントが、認証されていないユーザーをアプリのログインページにリダイレクトすることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-199">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="6e92e-200">SUT では、@no__t 0 ページは、 [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[authorizepage](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-200">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="6e92e-201">詳細については、「 [Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-201">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="6e92e-202">@No__t-0 テストでは、 [Allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を `false` に設定してリダイレクトを禁止するように[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)が設定されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-202">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="6e92e-203">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-203">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="6e92e-204">SUT によって返される状態コードは、予期された[httpstatuscode. リダイレクト](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [httpstatuscode. OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-204">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="6e92e-205">応答ヘッダーの `Location` ヘッダー値は、最後のログインページ応答ではなく `http://localhost/Identity/Account/Login` で開始されていることを確認し、`Location` ヘッダーが存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-205">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="6e92e-206">@No__t-0 の詳細については、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-206">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="6e92e-207">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="6e92e-207">Customize WebApplicationFactory</span></span>

<span data-ttu-id="6e92e-208">Web ホストの構成は、`WebApplicationFactory` から継承して1つ以上のカスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-208">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="6e92e-209">@No__t-0 から継承し、 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-209">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="6e92e-210">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-210">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="6e92e-211">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-211">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="6e92e-212">この方法については、「[統合テストのサンプル: テストアプリの組織](#test-app-organization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-212">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="6e92e-213">SUT のデータベースコンテキストは @no__t 0 のメソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-213">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e92e-214">テストアプリの `builder.ConfigureServices` コールバックは、アプリの @no__t コードが実行され*た後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-214">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="6e92e-215">アプリケーションのデータベースとは異なるデータベースをテストに使用するには、アプリのデータベースコンテキストを `builder.ConfigureServices` で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-215">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="6e92e-216">サンプルアプリでは、データベースコンテキストのサービス記述子を検索し、記述子を使用してサービス登録を削除します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-216">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="6e92e-217">次に、ファクトリは、テストにメモリ内データベースを使用する新しい `ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-217">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="6e92e-218">インメモリデータベースとは別のデータベースに接続するには、`UseInMemoryDatabase` 呼び出しを変更して、コンテキストを別のデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-218">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="6e92e-219">SQL Server テストデータベースを使用するには:</span><span class="sxs-lookup"><span data-stu-id="6e92e-219">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="6e92e-220">プロジェクトファイルで [Microsoft EntityFrameworkCore. SqlServer] @no__t NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-220">Reference the [Microsoft.EntityFrameworkCore.SqlServer]https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="6e92e-221">データベースへの接続文字列を使用して `UseSqlServer` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-221">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="6e92e-222">テストクラスでは、カスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-222">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="6e92e-223">次の例では、`IndexPageTests` クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-223">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="6e92e-224">サンプルアプリのクライアントは、`HttpClient` がリダイレクトされるのを防ぐように構成されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-224">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="6e92e-225">[セキュリティで保護されたエンドポイントのテスト](#test-a-secure-endpoint)に関するセクションで説明したように、これによって、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-225">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="6e92e-226">最初の応答は、これらのテストの多くで @no__t 0 のヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-226">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="6e92e-227">一般的なテストでは、`HttpClient` およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-227">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="6e92e-228">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-228">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="6e92e-229">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-229">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="6e92e-230">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6e92e-230">Make a request for the page.</span></span>
1. <span data-ttu-id="6e92e-231">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-231">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="6e92e-232">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6e92e-232">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="6e92e-233">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*ヘルパー/HttpClientExtensions*) と @no__t ヘルパーメソッド (*ヘルパー/htmlhelpers .Cs*) では、 [AngleSharp](https://anglesharp.github.io/)パーサーを使用して、による偽造防止チェックを処理します。次のメソッド:</span><span class="sxs-lookup"><span data-stu-id="6e92e-233">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="6e92e-234">`GetDocumentAsync` &ndash; は[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-234">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="6e92e-235">`GetDocumentAsync` は、元の @no__t に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-235">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="6e92e-236">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-236">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="6e92e-237">`HttpClient` @no__t の拡張メソッドでは、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [HttpRequestMessage](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-237">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="6e92e-238">@No__t のオーバーロード: 0 HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-238">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="6e92e-239">フォームの [送信] ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="6e92e-239">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="6e92e-240">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="6e92e-240">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="6e92e-241">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="6e92e-241">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="6e92e-242">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-242">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="6e92e-243">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-243">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="6e92e-244">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-244">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="6e92e-245">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-245">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="6e92e-246">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="6e92e-246">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="6e92e-247">テストメソッド内で追加の構成が必要な場合、 [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は、構成によってさらにカスタマイズされた[iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を含む新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-247">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="6e92e-248">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テストメソッドは、@no__t の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-248">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="6e92e-249">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-249">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="6e92e-250">@No__t-0 クラスの別のテストでは、データベース内のすべてのレコードを削除し、`Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-250">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="6e92e-251">SUT の `messages` フォームの最初の [削除] ボタンを選択すると、SUT に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-251">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="6e92e-252">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="6e92e-252">Client options</span></span>

<span data-ttu-id="6e92e-253">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)を示します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-253">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="6e92e-254">オプション</span><span class="sxs-lookup"><span data-stu-id="6e92e-254">Option</span></span> | <span data-ttu-id="6e92e-255">説明</span><span class="sxs-lookup"><span data-stu-id="6e92e-255">Description</span></span> | <span data-ttu-id="6e92e-256">既定</span><span class="sxs-lookup"><span data-stu-id="6e92e-256">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="6e92e-257">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="6e92e-257">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="6e92e-258">@No__t-0 インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-258">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="6e92e-259">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="6e92e-259">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="6e92e-260">@No__t 0 のインスタンスのベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-260">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="6e92e-261">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="6e92e-261">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="6e92e-262">@No__t-0 インスタンスが cookie を処理するかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-262">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="6e92e-263">Max自動リダイレクト</span><span class="sxs-lookup"><span data-stu-id="6e92e-263">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="6e92e-264">@No__t-0 インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-264">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="6e92e-265">7</span><span class="sxs-lookup"><span data-stu-id="6e92e-265">7</span></span> |

<span data-ttu-id="6e92e-266">@No__t-0 クラスを作成し、 [createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-266">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="6e92e-267">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="6e92e-267">Inject mock services</span></span>

<span data-ttu-id="6e92e-268">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-268">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="6e92e-269">**モックサービスを挿入するには、SUT に @no__t 2 のメソッドを持つ @no__t 1 クラスが必要です。**</span><span class="sxs-lookup"><span data-stu-id="6e92e-269">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="6e92e-270">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-270">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="6e92e-271">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-271">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="6e92e-272">*サービス/IQuoteService .cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-272">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="6e92e-273">*サービス/QuoteService (cs*):</span><span class="sxs-lookup"><span data-stu-id="6e92e-273">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="6e92e-274">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-274">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="6e92e-275">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-275">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="6e92e-276">*Pages/Index. .cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-276">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="6e92e-277">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-277">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="6e92e-278">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-278">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="6e92e-279">モックサービスは、アプリの `QuoteService` を、`TestQuoteService` というテストアプリによって提供されるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-279">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="6e92e-280">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-280">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="6e92e-281">`ConfigureTestServices` が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-281">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="6e92e-282">テストの実行中に生成されたマークアップには `TestQuoteService` によって指定された見積もりテキストが反映されるため、アサーションは次のように渡されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-282">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="6e92e-283">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="6e92e-283">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="6e92e-284">@No__t 0 のコンストラクターは、@no__t 3 のアセンブリ `System.Reflection.Assembly.FullName` と等しいキーを持つ統合テストを含むアセンブリの[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)を検索することによって、アプリの[コンテンツのルート](xref:fundamentals/index#content-root)パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-284">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="6e92e-285">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` は、ソリューションファイル ( *.sln*) の検索に戻り、@no__t 2 つのアセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-285">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="6e92e-286">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-286">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="6e92e-287">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="6e92e-287">Disable shadow copying</span></span>

<span data-ttu-id="6e92e-288">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-288">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="6e92e-289">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-289">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="6e92e-290">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xunit を使用し、正しい構成設定を含む xunit *. json*ファイルを含めることによって xunit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-290">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="6e92e-291">詳細については、「 [JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-291">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="6e92e-292">次のコンテンツを使用して、テストプロジェクトのルートに*xunit のランナー*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-292">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="6e92e-293">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="6e92e-293">Disposal of objects</span></span>

<span data-ttu-id="6e92e-294">@No__t 0 実装のテストが実行された後、xUnit が[Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[httpclient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-294">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="6e92e-295">開発者によってインスタンス化されたオブジェクトが破棄を必要とする場合は、@no__t 0 の実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-295">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="6e92e-296">詳細については、「 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-296">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="6e92e-297">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="6e92e-297">Integration tests sample</span></span>

<span data-ttu-id="6e92e-298">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-298">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="6e92e-299">アプリ</span><span class="sxs-lookup"><span data-stu-id="6e92e-299">App</span></span> | <span data-ttu-id="6e92e-300">プロジェクトディレクトリ</span><span class="sxs-lookup"><span data-stu-id="6e92e-300">Project directory</span></span> | <span data-ttu-id="6e92e-301">説明</span><span class="sxs-lookup"><span data-stu-id="6e92e-301">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="6e92e-302">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="6e92e-302">Message app (the SUT)</span></span> | <span data-ttu-id="6e92e-303">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="6e92e-303">*src/RazorPagesProject*</span></span> | <span data-ttu-id="6e92e-304">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-304">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="6e92e-305">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-305">Test app</span></span> | <span data-ttu-id="6e92e-306">*テスト/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="6e92e-306">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="6e92e-307">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-307">Used to integration test the SUT.</span></span> |

<span data-ttu-id="6e92e-308">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-308">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="6e92e-309">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-309">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="6e92e-310">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="6e92e-310">Message app (SUT) organization</span></span>

<span data-ttu-id="6e92e-311">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-311">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="6e92e-312">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-312">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="6e92e-313">メッセージは、2つのプロパティ (@no__t (キー) と `Text` (message)) を持つ @no__t 0 クラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-313">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="6e92e-314">@No__t-0 プロパティは必須で、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-314">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="6e92e-315">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-315">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="6e92e-316">アプリのデータベースコンテキストクラスにデータアクセス層 (DAL) が含まれています。 @no__t 0 (*data/AppDbContext .cs*) です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-316">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="6e92e-317">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-317">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="6e92e-318">アプリには、認証されたユーザーのみがアクセスできる @no__t 0 が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-318">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="6e92e-319">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-319">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="6e92e-320">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-320">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="6e92e-321">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-321">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="6e92e-322">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-322">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="6e92e-323">詳細については、「[インフラストラクチャの永続化層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テストコントローラーのロジック](/aspnet/core/mvc/controllers/testing)の設計 (例では、リポジトリパターンを実装します)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-323">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="6e92e-324">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="6e92e-324">Test app organization</span></span>

<span data-ttu-id="6e92e-325">テストアプリは、 *tests/RazorPagesProject*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-325">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="6e92e-326">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="6e92e-326">Test app directory</span></span> | <span data-ttu-id="6e92e-327">説明</span><span class="sxs-lookup"><span data-stu-id="6e92e-327">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="6e92e-328">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="6e92e-328">*BasicTests*</span></span> | <span data-ttu-id="6e92e-329">*BasicTests.cs*には、ルーティング用のテストメソッド、認証されていないユーザーによるセキュリティで保護されたページへのアクセス、GitHub ユーザープロファイルの取得、およびプロファイルのユーザーログインの確認が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-329">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="6e92e-330">*テストの統合*</span><span class="sxs-lookup"><span data-stu-id="6e92e-330">*IntegrationTests*</span></span> | <span data-ttu-id="6e92e-331">*IndexPageTests.cs*には、カスタム `WebApplicationFactory` クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-331">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="6e92e-332">*ヘルパー/ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="6e92e-332">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="6e92e-333">*Utilities.cs*には、テストデータを使用してデータベースをシード処理するために使用される `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-333">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="6e92e-334">*HtmlHelpers.cs*は、テストメソッドで使用するために、AngleSharp @no__t を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-334">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="6e92e-335">*HttpClientExtensions.cs*は、`SendAsync` のオーバーロードを使用して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-335">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="6e92e-336">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-336">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="6e92e-337">統合テストは、 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-337">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="6e92e-338">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、`TestHost` パッケージと @no__t パッケージは、テストアプリのプロジェクトファイルまたはテストの開発者構成で直接パッケージ参照を必要としません。アプリケーション.</span><span class="sxs-lookup"><span data-stu-id="6e92e-338">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="6e92e-339">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="6e92e-339">**Seeding the database for testing**</span></span>

<span data-ttu-id="6e92e-340">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-340">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="6e92e-341">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-341">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="6e92e-342">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-342">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="6e92e-343">SUT のデータベースコンテキストは @no__t 0 のメソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-343">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e92e-344">テストアプリの `builder.ConfigureServices` コールバックは、アプリの @no__t コードが実行され*た後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-344">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="6e92e-345">テストに別のデータベースを使用するには、アプリのデータベースコンテキストを `builder.ConfigureServices` で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-345">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="6e92e-346">詳細については、「 [WebApplicationFactory のカスタマイズ](#customize-webapplicationfactory)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-346">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6e92e-347">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-347">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="6e92e-348">ASP.NET Core では、単体テストフレームワークとテスト web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-348">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="6e92e-349">このトピックでは、単体テストの基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-349">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="6e92e-350">テストの概念を理解していない場合は、 [.Net Core の単体テストと .NET Standard に](/dotnet/core/testing/)関するトピックとそのリンク先を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-350">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="6e92e-351">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6e92e-351">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6e92e-352">このサンプルアプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-352">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="6e92e-353">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-353">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="6e92e-354">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="6e92e-354">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="6e92e-355">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="6e92e-355">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="6e92e-356">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-356">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="6e92e-357">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-357">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="6e92e-358">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="6e92e-358">Introduction to integration tests</span></span>

<span data-ttu-id="6e92e-359">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-359">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="6e92e-360">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-360">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="6e92e-361">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-361">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="6e92e-362">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-362">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="6e92e-363">データベース</span><span class="sxs-lookup"><span data-stu-id="6e92e-363">Database</span></span>
* <span data-ttu-id="6e92e-364">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="6e92e-364">File system</span></span>
* <span data-ttu-id="6e92e-365">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="6e92e-365">Network appliances</span></span>
* <span data-ttu-id="6e92e-366">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="6e92e-366">Request-response pipeline</span></span>

<span data-ttu-id="6e92e-367">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-367">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="6e92e-368">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-368">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="6e92e-369">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-369">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="6e92e-370">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-370">Require more code and data processing.</span></span>
* <span data-ttu-id="6e92e-371">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-371">Take longer to run.</span></span>

<span data-ttu-id="6e92e-372">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-372">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="6e92e-373">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-373">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="6e92e-374">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-374">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="6e92e-375">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-375">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="6e92e-376">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-376">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="6e92e-377">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-377">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="6e92e-378">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-378">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="6e92e-379">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="6e92e-379">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="6e92e-380">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-380">ASP.NET Core integration tests</span></span>

<span data-ttu-id="6e92e-381">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-381">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="6e92e-382">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-382">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="6e92e-383">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-383">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="6e92e-384">テストプロジェクトでは、SUT のテスト web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-384">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="6e92e-385">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-385">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="6e92e-386">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="6e92e-386">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="6e92e-387">SUT の web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-387">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="6e92e-388">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-388">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="6e92e-389">テストの*配置*ステップが実行されます。テストアプリは要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-389">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="6e92e-390">*Act*テストステップが実行されます。クライアントが要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-390">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="6e92e-391">*アサート*テストステップが実行されます。*実際*の応答は*成功*として検証されるか、*予期される*応答に基づいて*失敗*します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-391">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="6e92e-392">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-392">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="6e92e-393">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-393">The test results are reported.</span></span>

<span data-ttu-id="6e92e-394">通常、テスト web ホストは、テストの実行のためにアプリの通常の web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-394">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="6e92e-395">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-395">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="6e92e-396">テスト web ホストやメモリ内テストサーバー ([testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-396">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="6e92e-397">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-397">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="6e92e-398">@No__t-0 パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-398">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="6e92e-399">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-399">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="6e92e-400">テストの実行時に静的ファイルとページ/ビューが検出されるように、[コンテンツルート](xref:fundamentals/index#content-root)を SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-400">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="6e92e-401">@No__t-1 での SUT のブートストラップ化を効率化する[Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-401">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="6e92e-402">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-402">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="6e92e-403">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-403">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="6e92e-404">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-404">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="6e92e-405">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-405">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="6e92e-406">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-406">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="6e92e-407">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-407">The only difference is in how the tests are named.</span></span> <span data-ttu-id="6e92e-408">Razor Pages アプリでは、通常、ページエンドポイントのテストはページモデルクラスの後に名前が付けられます (たとえば、インデックスページのコンポーネントの統合をテストするには、`IndexPageTests` になります)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-408">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="6e92e-409">MVC アプリでは、テストは通常、コントローラークラス別に編成され、テスト対象のコントローラーの後に名前が付けられます (たとえば、Home コントローラーのコンポーネントの統合をテストするには、`HomeControllerTests`)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-409">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="6e92e-410">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="6e92e-410">Test app prerequisites</span></span>

<span data-ttu-id="6e92e-411">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-411">The test project must:</span></span>

* <span data-ttu-id="6e92e-412">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-412">Reference the following packages:</span></span>
  * [<span data-ttu-id="6e92e-413">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="6e92e-413">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="6e92e-414">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="6e92e-414">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="6e92e-415">プロジェクトファイルで Web SDK を指定します (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-415">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="6e92e-416">[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照するときは、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-416">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6e92e-417">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-417">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="6e92e-418">*テスト/RazorPagesProject/RazorPagesProject*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-418">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="6e92e-419">サンプルアプリでは、 [Xunit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-419">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="6e92e-420">xunit</span><span class="sxs-lookup"><span data-stu-id="6e92e-420">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="6e92e-421">xunit</span><span class="sxs-lookup"><span data-stu-id="6e92e-421">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="6e92e-422">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="6e92e-422">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="6e92e-423">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="6e92e-423">SUT environment</span></span>

<span data-ttu-id="6e92e-424">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-424">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="6e92e-425">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-425">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="6e92e-426">[Webapplicationfactory @ no__t-> 1:](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)統合テスト用の[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-426">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="6e92e-427">`TEntryPoint` は、SUT のエントリポイントクラス (通常は `Startup` クラス) です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-427">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="6e92e-428">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-428">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="6e92e-429">アプリエンドポイントの基本テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-429">Basic test of app endpoints</span></span>

<span data-ttu-id="6e92e-430">次のテストクラス (`BasicTests`) では、`WebApplicationFactory` を使用して、SUT をブートストラップし、テストメソッドに[Httpclient](/dotnet/api/system.net.http.httpclient)を指定します (@no__t 3)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-430">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="6e92e-431">メソッドは、応答ステータスコードが正常に実行されたかどうかを確認します (200-299 の範囲の状態コード)。また、複数のアプリページで @no__t は、`Content-Type` ヘッダーが-1 になります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-431">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="6e92e-432">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)が `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトを追跡し、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-432">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="6e92e-433">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-433">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="6e92e-434">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-434">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="6e92e-435">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-435">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="6e92e-436">セキュリティで保護されたエンドポイントをテストする</span><span class="sxs-lookup"><span data-stu-id="6e92e-436">Test a secure endpoint</span></span>

<span data-ttu-id="6e92e-437">@No__t-0 クラスのもう1つのテストは、セキュリティで保護されたエンドポイントが、認証されていないユーザーをアプリのログインページにリダイレクトすることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-437">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="6e92e-438">SUT では、@no__t 0 ページは、 [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[authorizepage](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-438">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="6e92e-439">詳細については、「 [Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-439">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="6e92e-440">@No__t-0 テストでは、 [Allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を `false` に設定してリダイレクトを禁止するように[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)が設定されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-440">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="6e92e-441">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-441">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="6e92e-442">SUT によって返される状態コードは、予期された[httpstatuscode. リダイレクト](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [httpstatuscode. OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-442">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="6e92e-443">応答ヘッダーの `Location` ヘッダー値は、最後のログインページ応答ではなく `http://localhost/Identity/Account/Login` で開始されていることを確認し、`Location` ヘッダーが存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-443">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="6e92e-444">@No__t-0 の詳細については、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-444">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="6e92e-445">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="6e92e-445">Customize WebApplicationFactory</span></span>

<span data-ttu-id="6e92e-446">Web ホストの構成は、`WebApplicationFactory` から継承して1つ以上のカスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-446">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="6e92e-447">@No__t-0 から継承し、 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-447">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="6e92e-448">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-448">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="6e92e-449">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-449">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="6e92e-450">この方法については、「[統合テストのサンプル: テストアプリの組織](#test-app-organization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-450">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="6e92e-451">テストクラスでは、カスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-451">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="6e92e-452">次の例では、`IndexPageTests` クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-452">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="6e92e-453">サンプルアプリのクライアントは、`HttpClient` がリダイレクトされるのを防ぐように構成されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-453">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="6e92e-454">[セキュリティで保護されたエンドポイントのテスト](#test-a-secure-endpoint)に関するセクションで説明したように、これによって、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-454">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="6e92e-455">最初の応答は、これらのテストの多くで @no__t 0 のヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-455">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="6e92e-456">一般的なテストでは、`HttpClient` およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-456">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="6e92e-457">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-457">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="6e92e-458">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-458">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="6e92e-459">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6e92e-459">Make a request for the page.</span></span>
1. <span data-ttu-id="6e92e-460">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-460">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="6e92e-461">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6e92e-461">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="6e92e-462">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*ヘルパー/HttpClientExtensions*) と @no__t ヘルパーメソッド (*ヘルパー/htmlhelpers .Cs*) では、 [AngleSharp](https://anglesharp.github.io/)パーサーを使用して、による偽造防止チェックを処理します。次のメソッド:</span><span class="sxs-lookup"><span data-stu-id="6e92e-462">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="6e92e-463">`GetDocumentAsync` &ndash; は[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-463">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="6e92e-464">`GetDocumentAsync` は、元の @no__t に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-464">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="6e92e-465">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-465">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="6e92e-466">`HttpClient` @no__t の拡張メソッドでは、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [HttpRequestMessage](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-466">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="6e92e-467">@No__t のオーバーロード: 0 HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-467">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="6e92e-468">フォームの [送信] ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="6e92e-468">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="6e92e-469">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="6e92e-469">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="6e92e-470">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="6e92e-470">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="6e92e-471">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-471">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="6e92e-472">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-472">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="6e92e-473">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-473">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="6e92e-474">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-474">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="6e92e-475">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="6e92e-475">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="6e92e-476">テストメソッド内で追加の構成が必要な場合、 [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は、構成によってさらにカスタマイズされた[iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を含む新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-476">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="6e92e-477">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テストメソッドは、@no__t の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-477">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="6e92e-478">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-478">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="6e92e-479">@No__t-0 クラスの別のテストでは、データベース内のすべてのレコードを削除し、`Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-479">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="6e92e-480">SUT の `messages` フォームの最初の [削除] ボタンを選択すると、SUT に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-480">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="6e92e-481">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="6e92e-481">Client options</span></span>

<span data-ttu-id="6e92e-482">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)を示します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-482">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="6e92e-483">オプション</span><span class="sxs-lookup"><span data-stu-id="6e92e-483">Option</span></span> | <span data-ttu-id="6e92e-484">説明</span><span class="sxs-lookup"><span data-stu-id="6e92e-484">Description</span></span> | <span data-ttu-id="6e92e-485">既定</span><span class="sxs-lookup"><span data-stu-id="6e92e-485">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="6e92e-486">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="6e92e-486">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="6e92e-487">@No__t-0 インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-487">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="6e92e-488">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="6e92e-488">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="6e92e-489">@No__t 0 のインスタンスのベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-489">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="6e92e-490">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="6e92e-490">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="6e92e-491">@No__t-0 インスタンスが cookie を処理するかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-491">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="6e92e-492">Max自動リダイレクト</span><span class="sxs-lookup"><span data-stu-id="6e92e-492">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="6e92e-493">@No__t-0 インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-493">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="6e92e-494">7</span><span class="sxs-lookup"><span data-stu-id="6e92e-494">7</span></span> |

<span data-ttu-id="6e92e-495">@No__t-0 クラスを作成し、 [createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-495">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="6e92e-496">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="6e92e-496">Inject mock services</span></span>

<span data-ttu-id="6e92e-497">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-497">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="6e92e-498">**モックサービスを挿入するには、SUT に @no__t 2 のメソッドを持つ @no__t 1 クラスが必要です。**</span><span class="sxs-lookup"><span data-stu-id="6e92e-498">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="6e92e-499">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-499">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="6e92e-500">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-500">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="6e92e-501">*サービス/IQuoteService .cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-501">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="6e92e-502">*サービス/QuoteService (cs*):</span><span class="sxs-lookup"><span data-stu-id="6e92e-502">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="6e92e-503">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-503">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="6e92e-504">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-504">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="6e92e-505">*Pages/Index. .cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-505">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="6e92e-506">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-506">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="6e92e-507">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-507">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="6e92e-508">モックサービスは、アプリの `QuoteService` を、`TestQuoteService` というテストアプリによって提供されるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-508">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="6e92e-509">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e92e-509">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="6e92e-510">`ConfigureTestServices` が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-510">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="6e92e-511">テストの実行中に生成されたマークアップには `TestQuoteService` によって指定された見積もりテキストが反映されるため、アサーションは次のように渡されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-511">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="6e92e-512">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="6e92e-512">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="6e92e-513">@No__t 0 のコンストラクターは、@no__t 3 のアセンブリ `System.Reflection.Assembly.FullName` と等しいキーを持つ統合テストを含むアセンブリの[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)を検索することによって、アプリの[コンテンツのルート](xref:fundamentals/index#content-root)パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-513">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="6e92e-514">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` は、ソリューションファイル ( *.sln*) の検索に戻り、@no__t 2 つのアセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-514">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="6e92e-515">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-515">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="6e92e-516">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="6e92e-516">Disable shadow copying</span></span>

<span data-ttu-id="6e92e-517">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-517">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="6e92e-518">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e92e-518">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="6e92e-519">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xunit を使用し、正しい構成設定を含む xunit *. json*ファイルを含めることによって xunit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-519">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="6e92e-520">詳細については、「 [JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-520">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="6e92e-521">次のコンテンツを使用して、テストプロジェクトのルートに*xunit のランナー*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-521">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="6e92e-522">Visual Studio を使用している場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピー]** する に設定します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-522">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="6e92e-523">Visual Studio を使用していない場合は、テストアプリのプロジェクトファイルに @no__t 0 ターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-523">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="6e92e-524">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="6e92e-524">Disposal of objects</span></span>

<span data-ttu-id="6e92e-525">@No__t 0 実装のテストが実行された後、xUnit が[Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[httpclient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-525">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="6e92e-526">開発者によってインスタンス化されたオブジェクトが破棄を必要とする場合は、@no__t 0 の実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-526">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="6e92e-527">詳細については、「 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-527">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="6e92e-528">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="6e92e-528">Integration tests sample</span></span>

<span data-ttu-id="6e92e-529">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-529">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="6e92e-530">アプリ</span><span class="sxs-lookup"><span data-stu-id="6e92e-530">App</span></span> | <span data-ttu-id="6e92e-531">プロジェクトディレクトリ</span><span class="sxs-lookup"><span data-stu-id="6e92e-531">Project directory</span></span> | <span data-ttu-id="6e92e-532">説明</span><span class="sxs-lookup"><span data-stu-id="6e92e-532">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="6e92e-533">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="6e92e-533">Message app (the SUT)</span></span> | <span data-ttu-id="6e92e-534">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="6e92e-534">*src/RazorPagesProject*</span></span> | <span data-ttu-id="6e92e-535">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-535">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="6e92e-536">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-536">Test app</span></span> | <span data-ttu-id="6e92e-537">*テスト/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="6e92e-537">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="6e92e-538">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-538">Used to integration test the SUT.</span></span> |

<span data-ttu-id="6e92e-539">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-539">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="6e92e-540">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-540">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="6e92e-541">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="6e92e-541">Message app (SUT) organization</span></span>

<span data-ttu-id="6e92e-542">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-542">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="6e92e-543">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="6e92e-543">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="6e92e-544">メッセージは、2つのプロパティ (@no__t (キー) と `Text` (message)) を持つ @no__t 0 クラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-544">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="6e92e-545">@No__t-0 プロパティは必須で、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-545">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="6e92e-546">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-546">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="6e92e-547">アプリのデータベースコンテキストクラスにデータアクセス層 (DAL) が含まれています。 @no__t 0 (*data/AppDbContext .cs*) です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-547">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="6e92e-548">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-548">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="6e92e-549">アプリには、認証されたユーザーのみがアクセスできる @no__t 0 が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-549">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="6e92e-550">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-550">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="6e92e-551">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-551">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="6e92e-552">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="6e92e-552">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="6e92e-553">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-553">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="6e92e-554">詳細については、「[インフラストラクチャの永続化層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テストコントローラーのロジック](/aspnet/core/mvc/controllers/testing)の設計 (例では、リポジトリパターンを実装します)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e92e-554">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="6e92e-555">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="6e92e-555">Test app organization</span></span>

<span data-ttu-id="6e92e-556">テストアプリは、 *tests/RazorPagesProject*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="6e92e-556">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="6e92e-557">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="6e92e-557">Test app directory</span></span> | <span data-ttu-id="6e92e-558">説明</span><span class="sxs-lookup"><span data-stu-id="6e92e-558">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="6e92e-559">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="6e92e-559">*BasicTests*</span></span> | <span data-ttu-id="6e92e-560">*BasicTests.cs*には、ルーティング用のテストメソッド、認証されていないユーザーによるセキュリティで保護されたページへのアクセス、GitHub ユーザープロファイルの取得、およびプロファイルのユーザーログインの確認が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-560">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="6e92e-561">*テストの統合*</span><span class="sxs-lookup"><span data-stu-id="6e92e-561">*IntegrationTests*</span></span> | <span data-ttu-id="6e92e-562">*IndexPageTests.cs*には、カスタム `WebApplicationFactory` クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-562">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="6e92e-563">*ヘルパー/ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="6e92e-563">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="6e92e-564">*Utilities.cs*には、テストデータを使用してデータベースをシード処理するために使用される `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e92e-564">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="6e92e-565">*HtmlHelpers.cs*は、テストメソッドで使用するために、AngleSharp @no__t を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-565">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="6e92e-566">*HttpClientExtensions.cs*は、`SendAsync` のオーバーロードを使用して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="6e92e-566">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="6e92e-567">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-567">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="6e92e-568">統合テストは、 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e92e-568">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="6e92e-569">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、`TestHost` パッケージと @no__t パッケージは、テストアプリのプロジェクトファイルまたはテストの開発者構成で直接パッケージ参照を必要としません。アプリケーション.</span><span class="sxs-lookup"><span data-stu-id="6e92e-569">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="6e92e-570">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="6e92e-570">**Seeding the database for testing**</span></span>

<span data-ttu-id="6e92e-571">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-571">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="6e92e-572">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="6e92e-572">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="6e92e-573">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="6e92e-573">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6e92e-574">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6e92e-574">Additional resources</span></span>

* [<span data-ttu-id="6e92e-575">単体テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-575">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="6e92e-576">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="6e92e-576">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="6e92e-577">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="6e92e-577">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="6e92e-578">テスト コントローラー</span><span class="sxs-lookup"><span data-stu-id="6e92e-578">Test controllers</span></span>](xref:mvc/controllers/testing)
