---
title: ASP.NET Core での統合テスト
author: guardrex
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/11/2019
uid: test/integration-tests
ms.openlocfilehash: eba54b891c4f2d9876f9db5a3d0394990584c146
ms.sourcegitcommit: a11f09c10ef3d4eeab7ae9ce993e7f30427741c1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149416"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="0e331-103">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="0e331-104">[Luke Latham](https://github.com/guardrex)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0e331-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0e331-105">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="0e331-106">ASP.NET Core では、単体テストフレームワークとテスト web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0e331-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="0e331-107">このトピックでは、単体テストの基本的な知識を前提とします。</span><span class="sxs-lookup"><span data-stu-id="0e331-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="0e331-108">テストの概念にあまり馴染みがない場合、[.NET Core と .NET Standard の単体テスト](/dotnet/core/testing/) のトピックとそこでリンクされているコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="0e331-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0e331-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0e331-110">サンプル アプリは、Razor ページ アプリで Razor ページの基本的な知識を前提としています。</span><span class="sxs-lookup"><span data-stu-id="0e331-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="0e331-111">Razor ページに不慣れな場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="0e331-112">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="0e331-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="0e331-113">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="0e331-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="0e331-114">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="0e331-115">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0e331-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="0e331-116">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="0e331-116">Introduction to integration tests</span></span>

<span data-ttu-id="0e331-117">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="0e331-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="0e331-118">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="0e331-119">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="0e331-120">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e331-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="0e331-121">データベース</span><span class="sxs-lookup"><span data-stu-id="0e331-121">Database</span></span>
* <span data-ttu-id="0e331-122">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="0e331-122">File system</span></span>
* <span data-ttu-id="0e331-123">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="0e331-123">Network appliances</span></span>
* <span data-ttu-id="0e331-124">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="0e331-124">Request-response pipeline</span></span>

<span data-ttu-id="0e331-125">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="0e331-126">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0e331-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="0e331-127">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="0e331-128">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-128">Require more code and data processing.</span></span>
* <span data-ttu-id="0e331-129">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="0e331-129">Take longer to run.</span></span>

<span data-ttu-id="0e331-130">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="0e331-131">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="0e331-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="0e331-132">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="0e331-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="0e331-133">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="0e331-134">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="0e331-135">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="0e331-136">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="0e331-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="0e331-137">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="0e331-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="0e331-138">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="0e331-139">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="0e331-140">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="0e331-141">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="0e331-142">テストプロジェクトでは、SUT のテスト web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="0e331-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="0e331-143">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="0e331-144">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="0e331-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="0e331-145">SUT の web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="0e331-146">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="0e331-147">テストの*配置*ステップが実行されます。テストアプリで要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="0e331-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="0e331-148">*Act*テストステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="0e331-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="0e331-149">*アサート*テストステップが実行されます。*実際*の応答は、*予期される*応答に基づいて、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="0e331-150">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="0e331-151">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-151">The test results are reported.</span></span>

<span data-ttu-id="0e331-152">通常、テスト web ホストは、テストの実行のためにアプリの通常の web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="0e331-153">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="0e331-154">テスト web ホストやメモリ内テストサーバー ([testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="0e331-155">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="0e331-156">パッケージ`Microsoft.AspNetCore.Mvc.Testing`は、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="0e331-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="0e331-157">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="0e331-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="0e331-158">テストの実行時に静的ファイルとページ/ビューが検出されるように、コンテンツルートを SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-158">Sets the content root to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="0e331-159">に`TestServer`は、SUT のブートストラップを効率化する[webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="0e331-160">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="0e331-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="0e331-161">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="0e331-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="0e331-162">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="0e331-163">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="0e331-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="0e331-164">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="0e331-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="0e331-165">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="0e331-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="0e331-166">Razor Pages アプリでは、ページエンドポイントのテストは通常、ページモデルクラスの後に名前が`IndexPageTests`付けられます (たとえば、[インデックス] ページのコンポーネントの統合をテストする場合)。</span><span class="sxs-lookup"><span data-stu-id="0e331-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="0e331-167">MVC アプリでは、テストは通常、コントローラークラスによって編成され、テストするコントローラーの後`HomeControllerTests`に名前が付けられます (たとえば、Home コントローラーのコンポーネント統合をテストする場合)。</span><span class="sxs-lookup"><span data-stu-id="0e331-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="0e331-168">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="0e331-168">Test app prerequisites</span></span>

<span data-ttu-id="0e331-169">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-169">The test project must:</span></span>

* <span data-ttu-id="0e331-170">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="0e331-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="0e331-171">プロジェクトファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="0e331-172">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="0e331-173">*テスト/RazorPagesProject/RazorPagesProject*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="0e331-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="0e331-174">サンプルアプリでは、 [Xunit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="0e331-175">xunit</span><span class="sxs-lookup"><span data-stu-id="0e331-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="0e331-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="0e331-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="0e331-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="0e331-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="0e331-178">テストでも Entity Framework Core が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="0e331-179">アプリの参照:</span><span class="sxs-lookup"><span data-stu-id="0e331-179">The app references:</span></span>

* [<span data-ttu-id="0e331-180">AspNetCore コア (Microsoft. 診断)</span><span class="sxs-lookup"><span data-stu-id="0e331-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="0e331-181">AspNetCore コア (Microsoft. Identity)</span><span class="sxs-lookup"><span data-stu-id="0e331-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="0e331-182">Microsoft EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0e331-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="0e331-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="0e331-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="0e331-184">Microsoft EntityFrameworkCore. ツール</span><span class="sxs-lookup"><span data-stu-id="0e331-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="0e331-185">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="0e331-185">SUT environment</span></span>

<span data-ttu-id="0e331-186">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="0e331-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="0e331-187">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="0e331-188">[Webapplicationfactory\<の >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 、統合テスト用の[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="0e331-189">`TEntryPoint`は、SUT のエントリポイントクラス (通常`Startup`はクラス) です。</span><span class="sxs-lookup"><span data-stu-id="0e331-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="0e331-190">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="0e331-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="0e331-191">アプリエンドポイントの基本テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-191">Basic test of app endpoints</span></span>

<span data-ttu-id="0e331-192">次のテスト クラスでは、`BasicTests`、`WebApplicationFactory` を使用して、SUT をブートストラップし、テストメソッドに [httpclient](/dotnet/api/system.net.http.httpclient) を提供します、`Get_EndpointsReturnSuccessAndCorrectContentType`。</span><span class="sxs-lookup"><span data-stu-id="0e331-192">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="0e331-193">メソッドは、応答の状態コードが成功したかどうか (200-299 の範囲の状態`Content-Type`コード) `text/html; charset=utf-8`を確認し、ヘッダーは複数のアプリページ用です。</span><span class="sxs-lookup"><span data-stu-id="0e331-193">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="0e331-194">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)は、リダイレクトを`HttpClient`自動的に追跡し、cookie を処理するのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e331-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="0e331-195">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="0e331-195">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="0e331-196">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="0e331-196">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="0e331-197">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-197">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="0e331-198">セキュリティで保護されたエンドポイントをテストする</span><span class="sxs-lookup"><span data-stu-id="0e331-198">Test a secure endpoint</span></span>

<span data-ttu-id="0e331-199">`BasicTests`クラスの別のテストでは、セキュリティで保護されたエンドポイントが、認証されていないユーザーをアプリのログインページにリダイレクトすることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-199">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="0e331-200">SUT では、ページ`/SecurePage`は[authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[authorizepage](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-200">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="0e331-201">詳細については、「 [Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-201">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="0e331-202">テストでは、 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)は[allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を次のように`false`設定することによってリダイレクトを許可しないように設定されています。 `Get_SecurePageRequiresAnAuthenticatedUser`</span><span class="sxs-lookup"><span data-stu-id="0e331-202">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="0e331-203">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-203">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="0e331-204">SUT によって返される状態コードは、予期された[httpstatuscode. リダイレクト](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [httpstatuscode. OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="0e331-204">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="0e331-205">応答ヘッダーの`http://localhost/Identity/Account/Login` `Location`ヘッダー値は、最後のログインページ応答ではなく、ヘッダーが存在しないことを確認するためにチェックされます。 `Location`</span><span class="sxs-lookup"><span data-stu-id="0e331-205">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="0e331-206">の詳細につい`WebApplicationFactoryClientOptions`ては、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-206">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="0e331-207">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0e331-207">Customize WebApplicationFactory</span></span>

<span data-ttu-id="0e331-208">Web ホストの構成は、から継承して1つ以上の`WebApplicationFactory`カスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-208">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="0e331-209">[ConfigureWebHost を](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)継承し、オーバーライドし`WebApplicationFactory`ます。</span><span class="sxs-lookup"><span data-stu-id="0e331-209">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="0e331-210">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-210">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="0e331-211">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、 `InitializeDbForTests`メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-211">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="0e331-212">このメソッドについては[、統合テストのサンプルを参照してください。テストアプリの](#test-app-organization)組織セクション。</span><span class="sxs-lookup"><span data-stu-id="0e331-212">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="0e331-213">テストクラスで`CustomWebApplicationFactory`カスタムを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-213">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="0e331-214">次の例で`IndexPageTests`は、クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-214">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="0e331-215">サンプルアプリのクライアントは、 `HttpClient`次のリダイレクトが行われないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-215">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="0e331-216">[セキュリティで保護されたエンドポイントのテスト](#test-a-secure-endpoint)に関するセクションで説明したように、これによって、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-216">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="0e331-217">最初の応答は、これらのテストの多くで、 `Location`ヘッダーを使用したリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="0e331-217">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="0e331-218">一般的なテストでは`HttpClient` 、およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="0e331-218">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="0e331-219">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-219">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0e331-220">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-220">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="0e331-221">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="0e331-221">Make a request for the page.</span></span>
1. <span data-ttu-id="0e331-222">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="0e331-222">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="0e331-223">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="0e331-223">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="0e331-224">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) および `GetDocumentAsync` ヘルパーメソッド (*Helpers/HtmlHelpers.cs*) では、[AngleSharp](https://anglesharp.github.io/) パーサーを使用してアンチ偽造を処理します。次のメソッドを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-224">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="0e331-225">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="0e331-225">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="0e331-226">`GetDocumentAsync`元`HttpResponseMessage`のに基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-226">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="0e331-227">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-227">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="0e331-228">`SendAsync`の拡張メソッドは`HttpClient` 、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [HttpRequestMessage](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="0e331-228">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="0e331-229">の`SendAsync`オーバーロードは、HTML 形式 (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0e331-229">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="0e331-230">フォームの [送信] ボタン`IHtmlElement`()</span><span class="sxs-lookup"><span data-stu-id="0e331-230">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="0e331-231">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="0e331-231">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="0e331-232">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="0e331-232">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="0e331-233">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="0e331-233">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="0e331-234">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="0e331-234">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="0e331-235">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="0e331-235">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="0e331-236">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="0e331-236">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="0e331-237">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="0e331-237">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="0e331-238">テストメソッド内で追加の構成が必要な場合、 [withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は`WebApplicationFactory` 、構成によってさらにカスタマイズされた[iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を使用して新しいを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e331-238">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="0e331-239">`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)のテストメソッドは、の`WithWebHostBuilder`使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e331-239">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="0e331-240">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="0e331-240">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="0e331-241">`IndexPageTests`クラス内の別のテストは、データベース内のすべてのレコードを削除し、メソッドの`Post_DeleteMessageHandler_ReturnsRedirectToRoot`前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-241">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="0e331-242">Sut で`messages`フォームの最初の [削除] ボタンを選択すると、sut に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="0e331-242">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="0e331-243">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="0e331-243">Client options</span></span>

<span data-ttu-id="0e331-244">次の表は、`HttpClient` インスタンスの作成時に使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e331-244">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="0e331-245">オプション</span><span class="sxs-lookup"><span data-stu-id="0e331-245">Option</span></span> | <span data-ttu-id="0e331-246">説明</span><span class="sxs-lookup"><span data-stu-id="0e331-246">Description</span></span> | <span data-ttu-id="0e331-247">既定値</span><span class="sxs-lookup"><span data-stu-id="0e331-247">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="0e331-248">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="0e331-248">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="0e331-249">`HttpClient`インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-249">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="0e331-250">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="0e331-250">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="0e331-251">インスタンスの`HttpClient`ベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-251">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="0e331-252">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="0e331-252">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="0e331-253">インスタンスが cookie を`HttpClient`処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-253">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="0e331-254">Max自動リダイレクト</span><span class="sxs-lookup"><span data-stu-id="0e331-254">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="0e331-255">インスタンスが従う必要がある`HttpClient`リダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-255">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="0e331-256">7</span><span class="sxs-lookup"><span data-stu-id="0e331-256">7</span></span> |

<span data-ttu-id="0e331-257">`WebApplicationFactoryClientOptions` クラスを作成し、[createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="0e331-257">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="0e331-258">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="0e331-258">Inject mock services</span></span>

<span data-ttu-id="0e331-259">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="0e331-259">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="0e331-260">**モックサービスを挿入するには、SUT が`Startup` `Startup.ConfigureServices`メソッドを持つクラスを持っている必要があります。**</span><span class="sxs-lookup"><span data-stu-id="0e331-260">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="0e331-261">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-261">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="0e331-262">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="0e331-262">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="0e331-263">*サービス/IQuoteService .cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-263">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="0e331-264">*サービス/QuoteService (cs*):</span><span class="sxs-lookup"><span data-stu-id="0e331-264">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="0e331-265">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-265">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="0e331-266">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-266">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="0e331-267">*Pages/Index. .cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-267">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="0e331-268">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-268">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="0e331-269">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-269">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="0e331-270">モックサービスは、アプリのを`QuoteService`テストアプリによって提供されるサービスに`TestQuoteService`置き換えます。これは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0e331-270">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="0e331-271">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-271">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="0e331-272">`ConfigureTestServices`が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-272">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="0e331-273">テストの実行中に生成されたマークアップは、に`TestQuoteService`よって指定された見積もりテキストを反映するため、アサーションは次のように合格します。</span><span class="sxs-lookup"><span data-stu-id="0e331-273">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="0e331-274">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="0e331-274">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="0e331-275">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリ `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリの [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリのコンテンツルートパスを推論します。</span><span class="sxs-lookup"><span data-stu-id="0e331-275">The `WebApplicationFactory` constructor infers the app content root path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="0e331-276">正しいキーを持つ属性が見つからない場合は、 `WebApplicationFactory`フォールバックしてソリューションファイル ( *.sln* `TEntryPoint` ) を検索し、アセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0e331-276">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="0e331-277">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-277">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="0e331-278">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="0e331-278">Disable shadow copying</span></span>

<span data-ttu-id="0e331-279">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-279">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="0e331-280">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-280">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="0e331-281">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xunit を使用し、正しい構成設定を含む xunit *. json*ファイルを含めることによって xunit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="0e331-281">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="0e331-282">詳細については、「 [JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-282">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="0e331-283">次のコンテンツを使用して、テストプロジェクトのルートに*xunit のランナー*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e331-283">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="0e331-284">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="0e331-284">Disposal of objects</span></span>

<span data-ttu-id="0e331-285">`IClassFixture`実装のテストが実行された後、xunit が[webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[httpclient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-285">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="0e331-286">開発者によってインスタンス化されたオブジェクトが破棄を`IClassFixture`必要とする場合は、実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="0e331-286">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="0e331-287">詳細については、「 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-287">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="0e331-288">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="0e331-288">Integration tests sample</span></span>

<span data-ttu-id="0e331-289">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-289">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="0e331-290">アプリ</span><span class="sxs-lookup"><span data-stu-id="0e331-290">App</span></span> | <span data-ttu-id="0e331-291">プロジェクトディレクトリ</span><span class="sxs-lookup"><span data-stu-id="0e331-291">Project directory</span></span> | <span data-ttu-id="0e331-292">説明</span><span class="sxs-lookup"><span data-stu-id="0e331-292">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="0e331-293">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="0e331-293">Message app (the SUT)</span></span> | <span data-ttu-id="0e331-294">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="0e331-294">*src/RazorPagesProject*</span></span> | <span data-ttu-id="0e331-295">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="0e331-295">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="0e331-296">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="0e331-296">Test app</span></span> | <span data-ttu-id="0e331-297">*テスト/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="0e331-297">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="0e331-298">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-298">Used to integration test the SUT.</span></span> |

<span data-ttu-id="0e331-299">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-299">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="0e331-300">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e331-300">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="0e331-301">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="0e331-301">Message app (SUT) organization</span></span>

<span data-ttu-id="0e331-302">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="0e331-302">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="0e331-303">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="0e331-303">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="0e331-304">メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-304">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="0e331-305">`Text`プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-305">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="0e331-306">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-306">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="0e331-307">アプリには、データベースコンテキストクラス`AppDbContext` (*data/appdbcontext .cs*) にデータアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-307">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="0e331-308">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-308">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="0e331-309">アプリには、 `/SecurePage`認証されたユーザーのみがアクセスできるが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-309">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="0e331-310">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="0e331-310">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="0e331-311">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-311">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="0e331-312">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="0e331-312">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="0e331-313">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="0e331-313">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="0e331-314">詳細については、「[インフラストラクチャの永続化層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テストコントローラーのロジック](/aspnet/core/mvc/controllers/testing)の設計 (例では、リポジトリパターンを実装します)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-314">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="0e331-315">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="0e331-315">Test app organization</span></span>

<span data-ttu-id="0e331-316">テストアプリは、 *tests/RazorPagesProject*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="0e331-316">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="0e331-317">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="0e331-317">Test app directory</span></span> | <span data-ttu-id="0e331-318">説明</span><span class="sxs-lookup"><span data-stu-id="0e331-318">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="0e331-319">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="0e331-319">*BasicTests*</span></span> | <span data-ttu-id="0e331-320">*BasicTests.cs*には、ルーティング用のテストメソッド、認証されていないユーザーによるセキュリティで保護されたページへのアクセス、GitHub ユーザープロファイルの取得、およびプロファイルのユーザーログインの確認が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-320">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="0e331-321">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="0e331-321">*IntegrationTests*</span></span> | <span data-ttu-id="0e331-322">*IndexPageTests.cs*には、カスタム`WebApplicationFactory`クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-322">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="0e331-323">*ヘルパー/ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="0e331-323">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="0e331-324">*Utilities.cs*には`InitializeDbForTests` 、テストデータを使用したデータベースのシード処理に使用するメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-324">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="0e331-325">*HtmlHelpers.cs*は、テストメソッドによって`IHtmlDocument`使用される AngleSharp を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="0e331-325">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="0e331-326">*HttpClientExtensions.cs*は、SUT `SendAsync`に要求を送信するためのオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="0e331-326">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="0e331-327">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="0e331-327">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="0e331-328">統合テストは、 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-328">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="0e331-329">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、パッケージと`TestHost` `TestServer`パッケージはテストアプリのプロジェクトファイルまたは開発者に直接パッケージ参照を必要としません。テストアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="0e331-329">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="0e331-330">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="0e331-330">**Seeding the database for testing**</span></span>

<span data-ttu-id="0e331-331">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-331">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="0e331-332">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-332">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="0e331-333">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="0e331-333">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0e331-334">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-334">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="0e331-335">ASP.NET Core では、単体テストフレームワークとテスト web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0e331-335">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="0e331-336">このトピックでは、単体テストの基本的な知識を前提とします。</span><span class="sxs-lookup"><span data-stu-id="0e331-336">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="0e331-337">テストの概念にあまり馴染みがない場合、[.NET Core と .NET Standard の単体テスト](/dotnet/core/testing/) のトピックとそこでリンクされているコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-337">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="0e331-338">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0e331-338">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0e331-339">サンプル アプリは、Razor ページ アプリで Razor ページの基本的な知識を前提としています。</span><span class="sxs-lookup"><span data-stu-id="0e331-339">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="0e331-340">Razor ページに不慣れな場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-340">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="0e331-341">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="0e331-341">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="0e331-342">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="0e331-342">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="0e331-343">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-343">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="0e331-344">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0e331-344">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="0e331-345">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="0e331-345">Introduction to integration tests</span></span>

<span data-ttu-id="0e331-346">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="0e331-346">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="0e331-347">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-347">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="0e331-348">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-348">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="0e331-349">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e331-349">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="0e331-350">データベース</span><span class="sxs-lookup"><span data-stu-id="0e331-350">Database</span></span>
* <span data-ttu-id="0e331-351">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="0e331-351">File system</span></span>
* <span data-ttu-id="0e331-352">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="0e331-352">Network appliances</span></span>
* <span data-ttu-id="0e331-353">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="0e331-353">Request-response pipeline</span></span>

<span data-ttu-id="0e331-354">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-354">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="0e331-355">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0e331-355">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="0e331-356">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-356">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="0e331-357">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-357">Require more code and data processing.</span></span>
* <span data-ttu-id="0e331-358">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="0e331-358">Take longer to run.</span></span>

<span data-ttu-id="0e331-359">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-359">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="0e331-360">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="0e331-360">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="0e331-361">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="0e331-361">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="0e331-362">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-362">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="0e331-363">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-363">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="0e331-364">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-364">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="0e331-365">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="0e331-365">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="0e331-366">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="0e331-366">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="0e331-367">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-367">ASP.NET Core integration tests</span></span>

<span data-ttu-id="0e331-368">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-368">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="0e331-369">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-369">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="0e331-370">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-370">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="0e331-371">テストプロジェクトでは、SUT のテスト web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="0e331-371">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="0e331-372">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-372">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="0e331-373">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="0e331-373">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="0e331-374">SUT の web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-374">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="0e331-375">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-375">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="0e331-376">テストの*配置*ステップが実行されます。テストアプリで要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="0e331-376">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="0e331-377">*Act*テストステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="0e331-377">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="0e331-378">*アサート*テストステップが実行されます。*実際*の応答は、*予期される*応答に基づいて、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-378">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="0e331-379">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-379">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="0e331-380">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-380">The test results are reported.</span></span>

<span data-ttu-id="0e331-381">通常、テスト web ホストは、テストの実行のためにアプリの通常の web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-381">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="0e331-382">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-382">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="0e331-383">テスト web ホストやメモリ内テストサーバー ([testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-383">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="0e331-384">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-384">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="0e331-385">パッケージ`Microsoft.AspNetCore.Mvc.Testing`は、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="0e331-385">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="0e331-386">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="0e331-386">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="0e331-387">テストの実行時に静的ファイルとページ/ビューが検出されるように、コンテンツルートを SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-387">Sets the content root to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="0e331-388">に`TestServer`は、SUT のブートストラップを効率化する[webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-388">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="0e331-389">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="0e331-389">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="0e331-390">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="0e331-390">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="0e331-391">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-391">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="0e331-392">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="0e331-392">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="0e331-393">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="0e331-393">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="0e331-394">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="0e331-394">The only difference is in how the tests are named.</span></span> <span data-ttu-id="0e331-395">Razor Pages アプリでは、ページエンドポイントのテストは通常、ページモデルクラスの後に名前が`IndexPageTests`付けられます (たとえば、[インデックス] ページのコンポーネントの統合をテストする場合)。</span><span class="sxs-lookup"><span data-stu-id="0e331-395">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="0e331-396">MVC アプリでは、テストは通常、コントローラークラスによって編成され、テストするコントローラーの後`HomeControllerTests`に名前が付けられます (たとえば、Home コントローラーのコンポーネント統合をテストする場合)。</span><span class="sxs-lookup"><span data-stu-id="0e331-396">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="0e331-397">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="0e331-397">Test app prerequisites</span></span>

<span data-ttu-id="0e331-398">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-398">The test project must:</span></span>

* <span data-ttu-id="0e331-399">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="0e331-399">Reference the following packages:</span></span>
  * [<span data-ttu-id="0e331-400">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="0e331-400">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="0e331-401">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="0e331-401">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="0e331-402">プロジェクトファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-402">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="0e331-403">[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照するときは、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-403">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="0e331-404">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-404">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="0e331-405">*テスト/RazorPagesProject/RazorPagesProject*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="0e331-405">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="0e331-406">サンプルアプリでは、 [Xunit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-406">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="0e331-407">xunit</span><span class="sxs-lookup"><span data-stu-id="0e331-407">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="0e331-408">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="0e331-408">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="0e331-409">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="0e331-409">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="0e331-410">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="0e331-410">SUT environment</span></span>

<span data-ttu-id="0e331-411">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="0e331-411">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="0e331-412">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-412">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="0e331-413">[Webapplicationfactory\<の >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 、統合テスト用の[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-413">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="0e331-414">`TEntryPoint`は、SUT のエントリポイントクラス (通常`Startup`はクラス) です。</span><span class="sxs-lookup"><span data-stu-id="0e331-414">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="0e331-415">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="0e331-415">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="0e331-416">アプリエンドポイントの基本テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-416">Basic test of app endpoints</span></span>

<span data-ttu-id="0e331-417">次のテスト クラスでは、`BasicTests`、`WebApplicationFactory` を使用して、SUT をブートストラップし、テストメソッドに [httpclient](/dotnet/api/system.net.http.httpclient) を提供します、`Get_EndpointsReturnSuccessAndCorrectContentType`。</span><span class="sxs-lookup"><span data-stu-id="0e331-417">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="0e331-418">メソッドは、応答の状態コードが成功したかどうか (200-299 の範囲の状態`Content-Type`コード) `text/html; charset=utf-8`を確認し、ヘッダーは複数のアプリページ用です。</span><span class="sxs-lookup"><span data-stu-id="0e331-418">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="0e331-419">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)は、リダイレクトを`HttpClient`自動的に追跡し、cookie を処理するのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e331-419">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="0e331-420">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="0e331-420">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="0e331-421">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="0e331-421">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="0e331-422">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-422">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="0e331-423">セキュリティで保護されたエンドポイントをテストする</span><span class="sxs-lookup"><span data-stu-id="0e331-423">Test a secure endpoint</span></span>

<span data-ttu-id="0e331-424">`BasicTests`クラスの別のテストでは、セキュリティで保護されたエンドポイントが、認証されていないユーザーをアプリのログインページにリダイレクトすることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-424">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="0e331-425">SUT では、ページ`/SecurePage`は[authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[authorizepage](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-425">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="0e331-426">詳細については、「 [Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-426">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="0e331-427">テストでは、 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)は[allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を次のように`false`設定することによってリダイレクトを許可しないように設定されています。 `Get_SecurePageRequiresAnAuthenticatedUser`</span><span class="sxs-lookup"><span data-stu-id="0e331-427">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="0e331-428">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="0e331-428">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="0e331-429">SUT によって返される状態コードは、予期された[httpstatuscode. リダイレクト](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [httpstatuscode. OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="0e331-429">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="0e331-430">応答ヘッダーの`http://localhost/Identity/Account/Login` `Location`ヘッダー値は、最後のログインページ応答ではなく、ヘッダーが存在しないことを確認するためにチェックされます。 `Location`</span><span class="sxs-lookup"><span data-stu-id="0e331-430">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="0e331-431">の詳細につい`WebApplicationFactoryClientOptions`ては、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-431">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="0e331-432">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0e331-432">Customize WebApplicationFactory</span></span>

<span data-ttu-id="0e331-433">Web ホストの構成は、から継承して1つ以上の`WebApplicationFactory`カスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-433">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="0e331-434">[ConfigureWebHost を](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)継承し、オーバーライドし`WebApplicationFactory`ます。</span><span class="sxs-lookup"><span data-stu-id="0e331-434">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="0e331-435">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-435">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="0e331-436">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、 `InitializeDbForTests`メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-436">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="0e331-437">このメソッドについては[、統合テストのサンプルを参照してください。テストアプリの](#test-app-organization)組織セクション。</span><span class="sxs-lookup"><span data-stu-id="0e331-437">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="0e331-438">テストクラスで`CustomWebApplicationFactory`カスタムを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-438">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="0e331-439">次の例で`IndexPageTests`は、クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-439">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="0e331-440">サンプルアプリのクライアントは、 `HttpClient`次のリダイレクトが行われないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-440">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="0e331-441">[セキュリティで保護されたエンドポイントのテスト](#test-a-secure-endpoint)に関するセクションで説明したように、これによって、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-441">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="0e331-442">最初の応答は、これらのテストの多くで、 `Location`ヘッダーを使用したリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="0e331-442">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="0e331-443">一般的なテストでは`HttpClient` 、およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="0e331-443">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="0e331-444">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-444">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0e331-445">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-445">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="0e331-446">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="0e331-446">Make a request for the page.</span></span>
1. <span data-ttu-id="0e331-447">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="0e331-447">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="0e331-448">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="0e331-448">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="0e331-449">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) および `GetDocumentAsync` ヘルパーメソッド (*Helpers/HtmlHelpers.cs*) では、[AngleSharp](https://anglesharp.github.io/) パーサーを使用してアンチ偽造を処理します。次のメソッドを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-449">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="0e331-450">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="0e331-450">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="0e331-451">`GetDocumentAsync`元`HttpResponseMessage`のに基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-451">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="0e331-452">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-452">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="0e331-453">`SendAsync`の拡張メソッドは`HttpClient` 、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [HttpRequestMessage](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="0e331-453">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="0e331-454">の`SendAsync`オーバーロードは、HTML 形式 (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0e331-454">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="0e331-455">フォームの [送信] ボタン`IHtmlElement`()</span><span class="sxs-lookup"><span data-stu-id="0e331-455">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="0e331-456">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="0e331-456">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="0e331-457">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="0e331-457">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="0e331-458">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="0e331-458">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="0e331-459">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="0e331-459">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="0e331-460">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="0e331-460">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="0e331-461">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="0e331-461">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="0e331-462">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="0e331-462">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="0e331-463">テストメソッド内で追加の構成が必要な場合、 [withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は`WebApplicationFactory` 、構成によってさらにカスタマイズされた[iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を使用して新しいを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e331-463">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="0e331-464">`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)のテストメソッドは、の`WithWebHostBuilder`使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e331-464">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="0e331-465">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="0e331-465">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="0e331-466">`IndexPageTests`クラス内の別のテストは、データベース内のすべてのレコードを削除し、メソッドの`Post_DeleteMessageHandler_ReturnsRedirectToRoot`前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e331-466">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="0e331-467">Sut で`messages`フォームの最初の [削除] ボタンを選択すると、sut に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="0e331-467">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="0e331-468">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="0e331-468">Client options</span></span>

<span data-ttu-id="0e331-469">次の表は、`HttpClient` インスタンスの作成時に使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e331-469">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="0e331-470">オプション</span><span class="sxs-lookup"><span data-stu-id="0e331-470">Option</span></span> | <span data-ttu-id="0e331-471">説明</span><span class="sxs-lookup"><span data-stu-id="0e331-471">Description</span></span> | <span data-ttu-id="0e331-472">既定値</span><span class="sxs-lookup"><span data-stu-id="0e331-472">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="0e331-473">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="0e331-473">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="0e331-474">`HttpClient`インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-474">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="0e331-475">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="0e331-475">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="0e331-476">インスタンスの`HttpClient`ベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-476">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="0e331-477">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="0e331-477">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="0e331-478">インスタンスが cookie を`HttpClient`処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-478">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="0e331-479">Max自動リダイレクト</span><span class="sxs-lookup"><span data-stu-id="0e331-479">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="0e331-480">インスタンスが従う必要がある`HttpClient`リダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-480">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="0e331-481">7</span><span class="sxs-lookup"><span data-stu-id="0e331-481">7</span></span> |

<span data-ttu-id="0e331-482">`WebApplicationFactoryClientOptions` クラスを作成し、[createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="0e331-482">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="0e331-483">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="0e331-483">Inject mock services</span></span>

<span data-ttu-id="0e331-484">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="0e331-484">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="0e331-485">**モックサービスを挿入するには、SUT が`Startup` `Startup.ConfigureServices`メソッドを持つクラスを持っている必要があります。**</span><span class="sxs-lookup"><span data-stu-id="0e331-485">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="0e331-486">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-486">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="0e331-487">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="0e331-487">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="0e331-488">*サービス/IQuoteService .cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-488">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="0e331-489">*サービス/QuoteService (cs*):</span><span class="sxs-lookup"><span data-stu-id="0e331-489">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="0e331-490">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-490">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="0e331-491">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-491">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="0e331-492">*Pages/Index. .cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-492">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="0e331-493">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-493">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="0e331-494">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-494">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="0e331-495">モックサービスは、アプリのを`QuoteService`テストアプリによって提供されるサービスに`TestQuoteService`置き換えます。これは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0e331-495">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="0e331-496">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e331-496">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="0e331-497">`ConfigureTestServices`が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-497">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="0e331-498">テストの実行中に生成されたマークアップは、に`TestQuoteService`よって指定された見積もりテキストを反映するため、アサーションは次のように合格します。</span><span class="sxs-lookup"><span data-stu-id="0e331-498">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="0e331-499">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="0e331-499">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="0e331-500">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリ `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリの [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリのコンテンツルートパスを推論します。</span><span class="sxs-lookup"><span data-stu-id="0e331-500">The `WebApplicationFactory` constructor infers the app content root path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="0e331-501">正しいキーを持つ属性が見つからない場合は、 `WebApplicationFactory`フォールバックしてソリューションファイル ( *.sln* `TEntryPoint` ) を検索し、アセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0e331-501">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="0e331-502">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-502">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="0e331-503">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="0e331-503">Disable shadow copying</span></span>

<span data-ttu-id="0e331-504">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-504">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="0e331-505">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e331-505">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="0e331-506">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xunit を使用し、正しい構成設定を含む xunit *. json*ファイルを含めることによって xunit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="0e331-506">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="0e331-507">詳細については、「 [JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-507">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="0e331-508">次のコンテンツを使用して、テストプロジェクトのルートに*xunit のランナー*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e331-508">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="0e331-509">Visual Studio を使用している場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピー]** する に設定します。</span><span class="sxs-lookup"><span data-stu-id="0e331-509">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="0e331-510">Visual Studio を使用していない`Content`場合は、テストアプリのプロジェクトファイルにターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e331-510">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="0e331-511">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="0e331-511">Disposal of objects</span></span>

<span data-ttu-id="0e331-512">`IClassFixture`実装のテストが実行された後、xunit が[webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[httpclient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-512">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="0e331-513">開発者によってインスタンス化されたオブジェクトが破棄を`IClassFixture`必要とする場合は、実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="0e331-513">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="0e331-514">詳細については、「 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-514">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="0e331-515">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="0e331-515">Integration tests sample</span></span>

<span data-ttu-id="0e331-516">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-516">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="0e331-517">アプリ</span><span class="sxs-lookup"><span data-stu-id="0e331-517">App</span></span> | <span data-ttu-id="0e331-518">プロジェクトディレクトリ</span><span class="sxs-lookup"><span data-stu-id="0e331-518">Project directory</span></span> | <span data-ttu-id="0e331-519">説明</span><span class="sxs-lookup"><span data-stu-id="0e331-519">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="0e331-520">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="0e331-520">Message app (the SUT)</span></span> | <span data-ttu-id="0e331-521">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="0e331-521">*src/RazorPagesProject*</span></span> | <span data-ttu-id="0e331-522">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="0e331-522">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="0e331-523">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="0e331-523">Test app</span></span> | <span data-ttu-id="0e331-524">*テスト/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="0e331-524">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="0e331-525">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-525">Used to integration test the SUT.</span></span> |

<span data-ttu-id="0e331-526">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="0e331-526">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="0e331-527">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e331-527">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="0e331-528">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="0e331-528">Message app (SUT) organization</span></span>

<span data-ttu-id="0e331-529">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="0e331-529">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="0e331-530">アプリのインデックスページ (*pages/index. cshtml*と*pages/index. cshtml. .cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="0e331-530">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="0e331-531">メッセージは、 `Id` (キー) `Message`と`Text` (message) の2つのプロパティを持つクラス (*Data/message .cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-531">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="0e331-532">`Text`プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="0e331-532">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="0e331-533">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-533">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="0e331-534">アプリには、データベースコンテキストクラス`AppDbContext` (*data/appdbcontext .cs*) にデータアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-534">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="0e331-535">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-535">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="0e331-536">アプリには、 `/SecurePage`認証されたユーザーのみがアクセスできるが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-536">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="0e331-537">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="0e331-537">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="0e331-538">このトピックでは、 [Xunit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e331-538">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="0e331-539">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="0e331-539">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="0e331-540">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="0e331-540">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="0e331-541">詳細については、「[インフラストラクチャの永続化層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テストコントローラーのロジック](/aspnet/core/mvc/controllers/testing)の設計 (例では、リポジトリパターンを実装します)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e331-541">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="0e331-542">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="0e331-542">Test app organization</span></span>

<span data-ttu-id="0e331-543">テストアプリは、 *tests/RazorPagesProject*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="0e331-543">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="0e331-544">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="0e331-544">Test app directory</span></span> | <span data-ttu-id="0e331-545">説明</span><span class="sxs-lookup"><span data-stu-id="0e331-545">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="0e331-546">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="0e331-546">*BasicTests*</span></span> | <span data-ttu-id="0e331-547">*BasicTests.cs*には、ルーティング用のテストメソッド、認証されていないユーザーによるセキュリティで保護されたページへのアクセス、GitHub ユーザープロファイルの取得、およびプロファイルのユーザーログインの確認が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-547">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="0e331-548">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="0e331-548">*IntegrationTests*</span></span> | <span data-ttu-id="0e331-549">*IndexPageTests.cs*には、カスタム`WebApplicationFactory`クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-549">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="0e331-550">*ヘルパー/ユーティリティ*</span><span class="sxs-lookup"><span data-stu-id="0e331-550">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="0e331-551">*Utilities.cs*には`InitializeDbForTests` 、テストデータを使用したデータベースのシード処理に使用するメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e331-551">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="0e331-552">*HtmlHelpers.cs*は、テストメソッドによって`IHtmlDocument`使用される AngleSharp を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="0e331-552">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="0e331-553">*HttpClientExtensions.cs*は、SUT `SendAsync`に要求を送信するためのオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="0e331-553">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="0e331-554">テストフレームワークは[Xunit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="0e331-554">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="0e331-555">統合テストは、 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e331-555">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="0e331-556">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、パッケージと`TestHost` `TestServer`パッケージはテストアプリのプロジェクトファイルまたは開発者に直接パッケージ参照を必要としません。テストアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="0e331-556">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="0e331-557">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="0e331-557">**Seeding the database for testing**</span></span>

<span data-ttu-id="0e331-558">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-558">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="0e331-559">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e331-559">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="0e331-560">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="0e331-560">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0e331-561">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0e331-561">Additional resources</span></span>

* [<span data-ttu-id="0e331-562">単体テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-562">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="0e331-563">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="0e331-563">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="0e331-564">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="0e331-564">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="0e331-565">テスト コントローラー</span><span class="sxs-lookup"><span data-stu-id="0e331-565">Test controllers</span></span>](xref:mvc/controllers/testing)
