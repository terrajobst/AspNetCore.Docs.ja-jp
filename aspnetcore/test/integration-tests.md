---
title: ASP.NET Core での統合テスト
author: guardrex
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: ccee8957a72da0eb5d870b1bd184ee1ea146a0e6
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693792"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="a0099-103">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="a0099-104">[Luke Latham](https://github.com/guardrex)、 [Javier Calvarro Jeannine](https://github.com/javiercn)、[上田 Smith](https://ardalis.com/)、 [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="a0099-104">By [Luke Latham](https://github.com/guardrex), [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0099-105">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0099-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="a0099-106">ASP.NET Core では、単体テストフレームワークとテスト Web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0099-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="a0099-107">このトピックでは、単体テストの基本的な知識を前提とします。</span><span class="sxs-lookup"><span data-stu-id="a0099-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="a0099-108">テストの概念を理解していない場合は、 [.NET Core の単体テストと .NET Standard](/dotnet/core/testing/) に関するトピックとそのリンク先を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="a0099-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a0099-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a0099-110">サンプル アプリは、Razor ページ アプリで Razor ページの基本的な知識を前提としています。</span><span class="sxs-lookup"><span data-stu-id="a0099-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="a0099-111">Razor ページに不慣れな場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="a0099-112">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="a0099-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="a0099-113">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="a0099-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="a0099-114">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="a0099-115">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0099-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="a0099-116">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="a0099-116">Introduction to integration tests</span></span>

<span data-ttu-id="a0099-117">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="a0099-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="a0099-118">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="a0099-119">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="a0099-120">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="a0099-121">データベース</span><span class="sxs-lookup"><span data-stu-id="a0099-121">Database</span></span>
* <span data-ttu-id="a0099-122">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="a0099-122">File system</span></span>
* <span data-ttu-id="a0099-123">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="a0099-123">Network appliances</span></span>
* <span data-ttu-id="a0099-124">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="a0099-124">Request-response pipeline</span></span>

<span data-ttu-id="a0099-125">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="a0099-126">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a0099-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="a0099-127">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="a0099-128">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-128">Require more code and data processing.</span></span>
* <span data-ttu-id="a0099-129">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="a0099-129">Take longer to run.</span></span>

<span data-ttu-id="a0099-130">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="a0099-131">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="a0099-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="a0099-132">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="a0099-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="a0099-133">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="a0099-134">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="a0099-135">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="a0099-136">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="a0099-137">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="a0099-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="a0099-138">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="a0099-139">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="a0099-140">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="a0099-141">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="a0099-142">テストプロジェクトでは、SUT のテスト Web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="a0099-143">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="a0099-144">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="a0099-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="a0099-145">SUT の Web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="a0099-146">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="a0099-147">テストの*配置*ステップが実行されます。テストアプリは要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="a0099-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="a0099-148">*Act*テストステップが実行されます。クライアントが要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="a0099-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="a0099-149">*アサート*テストステップが実行されます。*実際*の応答は*成功*として検証されるか、*予期される*応答に基づいて*失敗*します。</span><span class="sxs-lookup"><span data-stu-id="a0099-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="a0099-150">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="a0099-151">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-151">The test results are reported.</span></span>

<span data-ttu-id="a0099-152">通常、テスト Web ホストは、テストの実行のためにアプリの通常の Web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="a0099-153">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="a0099-154">テスト web ホストやメモリ内テストサーバー ([testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="a0099-155">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="a0099-156">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="a0099-157">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="a0099-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="a0099-158">テストの実行時に静的ファイルとページ/ビューが検出されるように、[コンテンツルート](xref:fundamentals/index#content-root)を SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="a0099-159">`TestServer`での SUT のブートストラップ化を効率化する[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a0099-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="a0099-160">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="a0099-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="a0099-161">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="a0099-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="a0099-162">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="a0099-163">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="a0099-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="a0099-164">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="a0099-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="a0099-165">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="a0099-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="a0099-166">Razor Pages アプリでは、ページエンドポイントのテストは通常、ページモデルクラスの後に名前が付けられます (たとえば、[インデックス] ページのコンポーネントの統合をテストする `IndexPageTests` ます)。</span><span class="sxs-lookup"><span data-stu-id="a0099-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="a0099-167">MVC アプリでは、テストは通常、コントローラークラス別に編成され、テストするコントローラーの後に名前が付けられます (たとえば、Home コントローラーのコンポーネント統合をテストする `HomeControllerTests`)。</span><span class="sxs-lookup"><span data-stu-id="a0099-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="a0099-168">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="a0099-168">Test app prerequisites</span></span>

<span data-ttu-id="a0099-169">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-169">The test project must:</span></span>

* <span data-ttu-id="a0099-170">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="a0099-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="a0099-171">プロジェクトファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="a0099-172">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="a0099-173">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="a0099-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="a0099-174">サンプルアプリでは、 [xUnit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="a0099-175">xunit</span><span class="sxs-lookup"><span data-stu-id="a0099-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="a0099-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="a0099-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="a0099-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="a0099-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="a0099-178">テストでも Entity Framework Core が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="a0099-179">アプリの参照:</span><span class="sxs-lookup"><span data-stu-id="a0099-179">The app references:</span></span>

* [<span data-ttu-id="a0099-180">AspNetCore コア (Microsoft. 診断)</span><span class="sxs-lookup"><span data-stu-id="a0099-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="a0099-181">AspNetCore コア (Microsoft. Identity)</span><span class="sxs-lookup"><span data-stu-id="a0099-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="a0099-182">Microsoft EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a0099-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="a0099-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="a0099-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="a0099-184">Microsoft EntityFrameworkCore. ツール</span><span class="sxs-lookup"><span data-stu-id="a0099-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="a0099-185">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="a0099-185">SUT environment</span></span>

<span data-ttu-id="a0099-186">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="a0099-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="a0099-187">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="a0099-188">[WebApplicationFactory \<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)は、統合テスト用の[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="a0099-189">`TEntryPoint` は、SUT のエントリポイントクラスです。通常は、`Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="a0099-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="a0099-190">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a0099-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="a0099-191">次のテストクラス `BasicTests`では、`WebApplicationFactory` を使用して、SUT をブートストラップし、 [HttpClient](/dotnet/api/system.net.http.httpclient)をテストメソッドに `Get_EndpointsReturnSuccessAndCorrectContentType`します。</span><span class="sxs-lookup"><span data-stu-id="a0099-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="a0099-192">メソッドは、応答ステータスコードが成功したかどうかを確認し (200-299 の範囲の状態コード)、`Content-Type` ヘッダーは複数のアプリページで `text/html; charset=utf-8` ます。</span><span class="sxs-lookup"><span data-stu-id="a0099-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="a0099-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)が `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトを追跡し、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="a0099-194">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="a0099-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="a0099-195">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="a0099-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="a0099-196">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="a0099-197">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="a0099-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="a0099-198">Web ホストの構成は、`WebApplicationFactory` から継承して1つ以上のカスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="a0099-199">`WebApplicationFactory` から継承し、 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="a0099-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="a0099-200">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="a0099-201">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-201">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="a0099-202">この方法については、「[統合テストのサンプル: テストアプリの組織](#test-app-organization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="a0099-203">SUT のデータベースコンテキストは `Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a0099-204">テストアプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行され*た後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="a0099-205">実行順序は、ASP.NET Core 3.0 のリリースでの[汎用ホスト](xref:fundamentals/host/generic-host)の互換性に影響する変更点です。</span><span class="sxs-lookup"><span data-stu-id="a0099-205">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="a0099-206">アプリケーションのデータベースとは異なるデータベースをテストに使用するには、アプリケーションのデータベースコンテキストを `builder.ConfigureServices`で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-206">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="a0099-207">サンプルアプリでは、データベースコンテキストのサービス記述子を検索し、記述子を使用してサービス登録を削除します。</span><span class="sxs-lookup"><span data-stu-id="a0099-207">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="a0099-208">次に、ファクトリは、テストにメモリ内データベースを使用する新しい `ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="a0099-208">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="a0099-209">インメモリデータベースとは別のデータベースに接続するには、`UseInMemoryDatabase` 呼び出しを変更して、コンテキストを別のデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="a0099-209">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="a0099-210">SQL Server テストデータベースを使用するには:</span><span class="sxs-lookup"><span data-stu-id="a0099-210">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="a0099-211">プロジェクトファイルで、 [Microsoft EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="a0099-211">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="a0099-212">データベースへの接続文字列を使用して `UseSqlServer` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a0099-212">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="a0099-213">テストクラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-213">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="a0099-214">次の例では、`IndexPageTests` クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-214">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="a0099-215">サンプルアプリのクライアントは、`HttpClient` がリダイレクトされないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-215">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="a0099-216">「[モック認証](#mock-authentication)」セクションで後ほど説明するように、これにより、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-216">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="a0099-217">最初の応答は、これらのテストの多くで `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="a0099-217">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="a0099-218">一般的なテストでは、`HttpClient` およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-218">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="a0099-219">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-219">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="a0099-220">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-220">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="a0099-221">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="a0099-221">Make a request for the page.</span></span>
1. <span data-ttu-id="a0099-222">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="a0099-222">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="a0099-223">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="a0099-223">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="a0099-224">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパーメソッド (*Helpers/HtmlHelpers.cs*) では、 [AngleSharp](https://anglesharp.github.io/)パーサーを使用して、による偽造防止チェックを処理します。次のメソッド:</span><span class="sxs-lookup"><span data-stu-id="a0099-224">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="a0099-225">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="a0099-225">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="a0099-226">`GetDocumentAsync` は、元の `HttpResponseMessage`に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-226">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="a0099-227">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-227">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="a0099-228">`HttpClient` の `SendAsync` 拡張メソッドは、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="a0099-228">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="a0099-229">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="a0099-229">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="a0099-230">フォームの [送信] ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="a0099-230">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="a0099-231">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="a0099-231">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="a0099-232">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="a0099-232">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="a0099-233">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="a0099-233">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="a0099-234">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a0099-234">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="a0099-235">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a0099-235">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="a0099-236">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="a0099-236">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="a0099-237">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="a0099-237">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="a0099-238">テストメソッド内で追加の構成が必要な場合、 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は、構成によってさらにカスタマイズされた[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を含む新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="a0099-238">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="a0099-239">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テストメソッドは、`WithWebHostBuilder`の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0099-239">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="a0099-240">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="a0099-240">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="a0099-241">`IndexPageTests` クラス内の別のテストでは、データベース内のすべてのレコードを削除し、`Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0099-241">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="a0099-242">SUT の `messages` フォームの最初の [削除] ボタンを選択すると、SUT に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="a0099-242">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="a0099-243">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="a0099-243">Client options</span></span>

<span data-ttu-id="a0099-244">次の表は、`HttpClient` インスタンスの作成時に使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0099-244">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="a0099-245">オプション</span><span class="sxs-lookup"><span data-stu-id="a0099-245">Option</span></span> | <span data-ttu-id="a0099-246">説明</span><span class="sxs-lookup"><span data-stu-id="a0099-246">Description</span></span> | <span data-ttu-id="a0099-247">[既定値]</span><span class="sxs-lookup"><span data-stu-id="a0099-247">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="a0099-248">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="a0099-248">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="a0099-249">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-249">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="a0099-250">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="a0099-250">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="a0099-251">`HttpClient` インスタンスのベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-251">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="a0099-252">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="a0099-252">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="a0099-253">`HttpClient` インスタンスが cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-253">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="a0099-254">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="a0099-254">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="a0099-255">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-255">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="a0099-256">7</span><span class="sxs-lookup"><span data-stu-id="a0099-256">7</span></span> |

<span data-ttu-id="a0099-257">`WebApplicationFactoryClientOptions` クラスを作成し、 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="a0099-257">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="a0099-258">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="a0099-258">Inject mock services</span></span>

<span data-ttu-id="a0099-259">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="a0099-259">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="a0099-260">**モックサービスを挿入するには、SUT が `Startup.ConfigureServices` メソッドを持つ `Startup` クラスを持っている必要があります。**</span><span class="sxs-lookup"><span data-stu-id="a0099-260">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="a0099-261">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-261">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="a0099-262">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-262">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="a0099-263">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-263">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="a0099-264">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-264">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="a0099-265">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-265">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="a0099-266">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-266">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="a0099-267">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-267">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="a0099-268">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-268">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="a0099-269">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-269">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="a0099-270">モックサービスは、アプリの `QuoteService` を、`TestQuoteService`と呼ばれるテストアプリによって提供されるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0099-270">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="a0099-271">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-271">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="a0099-272">`ConfigureTestServices` が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-272">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="a0099-273">テストの実行中に生成されたマークアップには `TestQuoteService`によって指定された引用符が反映されるため、アサーションは次のように渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-273">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="a0099-274">モック認証</span><span class="sxs-lookup"><span data-stu-id="a0099-274">Mock authentication</span></span>

<span data-ttu-id="a0099-275">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0099-275">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="a0099-276">認証されていないユーザーをアプリのログインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="a0099-276">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="a0099-277">認証されたユーザーのコンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="a0099-277">Returns content for an authenticated user.</span></span>

<span data-ttu-id="a0099-278">SUT では、`/SecurePage` ページは、 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-278">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="a0099-279">詳細については、「[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-279">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="a0099-280">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を `false`に設定してリダイレクトを許可しないように[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)が設定されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-280">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="a0099-281">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-281">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="a0099-282">SUT によって返される状態コードは、予期された[HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="a0099-282">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="a0099-283">応答ヘッダーの `Location` ヘッダー値は、最後のログインページ応答ではなく `http://localhost/Identity/Account/Login`で開始され、`Location` ヘッダーが存在しないことを確認するためにチェックされます。</span><span class="sxs-lookup"><span data-stu-id="a0099-283">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="a0099-284">テストアプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-284">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="a0099-285">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-285">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="a0099-286">`TestAuthHandler` は、`AddAuthentication` が `ConfigureTestServices`に登録されている `Test` に認証スキームが設定されている場合に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-286">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="a0099-287">`WebApplicationFactoryClientOptions`の詳細については、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-287">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="a0099-288">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="a0099-288">Set the environment</span></span>

<span data-ttu-id="a0099-289">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-289">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="a0099-290">SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0099-290">To override the SUT's environment:</span></span>

* <span data-ttu-id="a0099-291">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing`などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-291">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="a0099-292">テストアプリの `CreateHostBuilder` をオーバーライドして、`ASPNETCORE`で始まる環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="a0099-292">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="a0099-293">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="a0099-293">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="a0099-294">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリ `System.Reflection.Assembly.FullName`と同じキーを持つ統合テストを含むアセンブリの[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)を検索することによって、アプリの[コンテンツルート](xref:fundamentals/index#content-root)パスを推論します。</span><span class="sxs-lookup"><span data-stu-id="a0099-294">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="a0099-295">正しいキーを持つ属性が見つからない場合 `WebApplicationFactory`、ソリューションファイル ( *.sln*) の検索にフォールバックし、`TEntryPoint` アセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="a0099-295">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="a0099-296">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-296">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="a0099-297">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="a0099-297">Disable shadow copying</span></span>

<span data-ttu-id="a0099-298">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-298">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="a0099-299">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-299">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="a0099-300">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xUnit を使用し、正しい構成設定を含む  *xunit.runner.json*ファイルを含めることによって xUnit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="a0099-300">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="a0099-301">詳細については、「[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-301">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="a0099-302">次のコンテンツを使用して、テストプロジェクトのルートに*xunit.runner.json*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="a0099-302">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="a0099-303">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="a0099-303">Disposal of objects</span></span>

<span data-ttu-id="a0099-304">`IClassFixture` 実装のテストが実行された後、xUnit が[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[HttpClient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-304">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="a0099-305">開発者によってインスタンス化されたオブジェクトが破棄を必要とする場合は、`IClassFixture` の実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="a0099-305">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="a0099-306">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-306">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="a0099-307">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="a0099-307">Integration tests sample</span></span>

<span data-ttu-id="a0099-308">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-308">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="a0099-309">アプリ</span><span class="sxs-lookup"><span data-stu-id="a0099-309">App</span></span> | <span data-ttu-id="a0099-310">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="a0099-310">Project directory</span></span> | <span data-ttu-id="a0099-311">説明</span><span class="sxs-lookup"><span data-stu-id="a0099-311">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="a0099-312">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="a0099-312">Message app (the SUT)</span></span> | <span data-ttu-id="a0099-313">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="a0099-313">*src/RazorPagesProject*</span></span> | <span data-ttu-id="a0099-314">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="a0099-314">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="a0099-315">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="a0099-315">Test app</span></span> | <span data-ttu-id="a0099-316">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="a0099-316">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="a0099-317">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-317">Used to integration test the SUT.</span></span> |

<span data-ttu-id="a0099-318">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-318">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="a0099-319">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a0099-319">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="a0099-320">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="a0099-320">Message app (SUT) organization</span></span>

<span data-ttu-id="a0099-321">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="a0099-321">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="a0099-322">アプリのインデックスページ (*Pages/Index.cshtml*と*Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="a0099-322">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="a0099-323">メッセージは、`Id` (キー) と `Text` (message) の2つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-323">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="a0099-324">`Text` プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-324">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="a0099-325">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-325">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="a0099-326">アプリには、データベースコンテキストクラス (Data/AppDbContext.cs) にデータアクセス層 (DAL) が含まれています。これは、`AppDbContext` (*Data/AppDbContext.cs*) です。</span><span class="sxs-lookup"><span data-stu-id="a0099-326">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="a0099-327">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-327">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="a0099-328">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-328">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="a0099-329">&#8224;EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="a0099-329">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="a0099-330">このトピックでは、 [xUnit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-330">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="a0099-331">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="a0099-331">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="a0099-332">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a0099-332">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="a0099-333">詳細については、「[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と「[コントローラーロジックのテスト](/aspnet/core/mvc/controllers/testing)」を参照してください(例では、リポジトリパターンを実装します)」。</span><span class="sxs-lookup"><span data-stu-id="a0099-333">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="a0099-334">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="a0099-334">Test app organization</span></span>

<span data-ttu-id="a0099-335">テストアプリは、 *tests/RazorPagesProject.Tests*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="a0099-335">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="a0099-336">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="a0099-336">Test app directory</span></span> | <span data-ttu-id="a0099-337">説明</span><span class="sxs-lookup"><span data-stu-id="a0099-337">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="a0099-338">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="a0099-338">*AuthTests*</span></span> | <span data-ttu-id="a0099-339">次のテストメソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-339">Contains test methods for:</span></span><ul><li><span data-ttu-id="a0099-340">認証されていないユーザーによるセキュリティで保護されたページへのアクセス。</span><span class="sxs-lookup"><span data-stu-id="a0099-340">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="a0099-341">認証されたユーザーがモック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>を使用してセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="a0099-341">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="a0099-342">GitHub ユーザープロファイルを取得し、プロファイルのユーザーログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="a0099-342">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="a0099-343">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="a0099-343">*BasicTests*</span></span> | <span data-ttu-id="a0099-344">ルーティングおよびコンテンツタイプのテストメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-344">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="a0099-345">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="a0099-345">*IntegrationTests*</span></span> | <span data-ttu-id="a0099-346">カスタム `WebApplicationFactory` クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-346">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="a0099-347">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="a0099-347">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="a0099-348">*Utilities.cs*には、テストデータを使用してデータベースをシード処理するために使用する `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-348">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="a0099-349">*HtmlHelpers.cs*は、テストメソッドで使用するために AngleSharp `IHtmlDocument` を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="a0099-349">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="a0099-350">*HttpClientExtensions.cs*は、`SendAsync` のオーバーロードを使用して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="a0099-350">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="a0099-351">テストフレームワークは[xUnit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="a0099-351">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="a0099-352">統合テストは、 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-352">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="a0099-353">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、`TestHost` および `TestServer` パッケージはテストアプリのプロジェクトファイルまたはテストの開発者構成で直接パッケージ参照を必要としません。アプリケーション.</span><span class="sxs-lookup"><span data-stu-id="a0099-353">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="a0099-354">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="a0099-354">**Seeding the database for testing**</span></span>

<span data-ttu-id="a0099-355">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-355">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="a0099-356">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-356">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="a0099-357">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="a0099-357">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="a0099-358">SUT のデータベースコンテキストは `Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-358">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a0099-359">テストアプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行され*た後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-359">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="a0099-360">テストに別のデータベースを使用するには、アプリケーションのデータベースコンテキストを `builder.ConfigureServices`で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-360">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="a0099-361">詳細については、「[WebApplicationFactory のカスタマイズ](#customize-webapplicationfactory)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-361">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0099-362">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0099-362">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="a0099-363">ASP.NET Core では、単体テストフレームワークとテスト Web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0099-363">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="a0099-364">このトピックでは、単体テストの基本的な知識を前提とします。</span><span class="sxs-lookup"><span data-stu-id="a0099-364">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="a0099-365">テストの概念を理解していない場合は、 [.NET Core の単体テストと .NET Standard](/dotnet/core/testing/) に関するトピックとそのリンク先を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-365">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="a0099-366">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a0099-366">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a0099-367">サンプル アプリは、Razor ページ アプリで Razor ページの基本的な知識を前提としています。</span><span class="sxs-lookup"><span data-stu-id="a0099-367">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="a0099-368">Razor ページに不慣れな場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-368">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="a0099-369">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="a0099-369">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="a0099-370">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="a0099-370">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="a0099-371">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-371">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="a0099-372">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0099-372">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="a0099-373">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="a0099-373">Introduction to integration tests</span></span>

<span data-ttu-id="a0099-374">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="a0099-374">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="a0099-375">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-375">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="a0099-376">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-376">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="a0099-377">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-377">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="a0099-378">データベース</span><span class="sxs-lookup"><span data-stu-id="a0099-378">Database</span></span>
* <span data-ttu-id="a0099-379">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="a0099-379">File system</span></span>
* <span data-ttu-id="a0099-380">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="a0099-380">Network appliances</span></span>
* <span data-ttu-id="a0099-381">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="a0099-381">Request-response pipeline</span></span>

<span data-ttu-id="a0099-382">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-382">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="a0099-383">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a0099-383">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="a0099-384">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-384">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="a0099-385">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-385">Require more code and data processing.</span></span>
* <span data-ttu-id="a0099-386">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="a0099-386">Take longer to run.</span></span>

<span data-ttu-id="a0099-387">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-387">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="a0099-388">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="a0099-388">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="a0099-389">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="a0099-389">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="a0099-390">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-390">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="a0099-391">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-391">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="a0099-392">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-392">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="a0099-393">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-393">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="a0099-394">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="a0099-394">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="a0099-395">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-395">ASP.NET Core integration tests</span></span>

<span data-ttu-id="a0099-396">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-396">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="a0099-397">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-397">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="a0099-398">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-398">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="a0099-399">テストプロジェクトでは、SUT のテスト Web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-399">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="a0099-400">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-400">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="a0099-401">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="a0099-401">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="a0099-402">SUT の Web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-402">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="a0099-403">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-403">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="a0099-404">テストの*配置*ステップが実行されます。テストアプリは要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="a0099-404">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="a0099-405">*Act*テストステップが実行されます。クライアントが要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="a0099-405">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="a0099-406">*アサート*テストステップが実行されます。*実際*の応答は*成功*として検証されるか、*予期される*応答に基づいて*失敗*します。</span><span class="sxs-lookup"><span data-stu-id="a0099-406">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="a0099-407">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-407">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="a0099-408">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-408">The test results are reported.</span></span>

<span data-ttu-id="a0099-409">通常、テスト Web ホストは、テストの実行のためにアプリの通常の Web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-409">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="a0099-410">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-410">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="a0099-411">テスト web ホストやメモリ内テストサーバー ([testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-411">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="a0099-412">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-412">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="a0099-413">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-413">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="a0099-414">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="a0099-414">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="a0099-415">テストの実行時に静的ファイルとページ/ビューが検出されるように、[コンテンツルート](xref:fundamentals/index#content-root)を SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-415">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="a0099-416">`TestServer`での SUT のブートストラップ化を効率化する[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a0099-416">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="a0099-417">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="a0099-417">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="a0099-418">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="a0099-418">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="a0099-419">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-419">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="a0099-420">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="a0099-420">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="a0099-421">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="a0099-421">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="a0099-422">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="a0099-422">The only difference is in how the tests are named.</span></span> <span data-ttu-id="a0099-423">Razor Pages アプリでは、ページエンドポイントのテストは通常、ページモデルクラスの後に名前が付けられます (たとえば、[インデックス] ページのコンポーネントの統合をテストする `IndexPageTests` ます)。</span><span class="sxs-lookup"><span data-stu-id="a0099-423">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="a0099-424">MVC アプリでは、テストは通常、コントローラークラス別に編成され、テストするコントローラーの後に名前が付けられます (たとえば、Home コントローラーのコンポーネント統合をテストする `HomeControllerTests`)。</span><span class="sxs-lookup"><span data-stu-id="a0099-424">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="a0099-425">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="a0099-425">Test app prerequisites</span></span>

<span data-ttu-id="a0099-426">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-426">The test project must:</span></span>

* <span data-ttu-id="a0099-427">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="a0099-427">Reference the following packages:</span></span>
  * [<span data-ttu-id="a0099-428">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="a0099-428">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="a0099-429">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="a0099-429">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="a0099-430">プロジェクトファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-430">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="a0099-431">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するときは、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-431">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a0099-432">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-432">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="a0099-433">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="a0099-433">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="a0099-434">サンプルアプリでは、 [xUnit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-434">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="a0099-435">xunit</span><span class="sxs-lookup"><span data-stu-id="a0099-435">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="a0099-436">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="a0099-436">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="a0099-437">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="a0099-437">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="a0099-438">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="a0099-438">SUT environment</span></span>

<span data-ttu-id="a0099-439">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="a0099-439">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="a0099-440">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-440">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="a0099-441">[WebApplicationFactory \<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)は、統合テスト用の[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-441">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="a0099-442">`TEntryPoint` は、SUT のエントリポイントクラスです。通常は、`Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="a0099-442">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="a0099-443">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a0099-443">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="a0099-444">次のテストクラス `BasicTests`では、`WebApplicationFactory` を使用して、SUT をブートストラップし、 [HttpClient](/dotnet/api/system.net.http.httpclient)をテストメソッドに `Get_EndpointsReturnSuccessAndCorrectContentType`します。</span><span class="sxs-lookup"><span data-stu-id="a0099-444">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="a0099-445">メソッドは、応答ステータスコードが成功したかどうかを確認し (200-299 の範囲の状態コード)、`Content-Type` ヘッダーは複数のアプリページで `text/html; charset=utf-8` ます。</span><span class="sxs-lookup"><span data-stu-id="a0099-445">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="a0099-446">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)が `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトを追跡し、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-446">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="a0099-447">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="a0099-447">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="a0099-448">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="a0099-448">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="a0099-449">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-449">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="a0099-450">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="a0099-450">Customize WebApplicationFactory</span></span>

<span data-ttu-id="a0099-451">Web ホストの構成は、`WebApplicationFactory` から継承して1つ以上のカスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-451">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="a0099-452">`WebApplicationFactory` から継承し、 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="a0099-452">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="a0099-453">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-453">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="a0099-454">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-454">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="a0099-455">この方法については、「[統合テストのサンプル: テストアプリの組織](#test-app-organization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-455">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="a0099-456">テストクラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-456">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="a0099-457">次の例では、`IndexPageTests` クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-457">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="a0099-458">サンプルアプリのクライアントは、`HttpClient` がリダイレクトされないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-458">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="a0099-459">「[モック認証](#mock-authentication)」セクションで後ほど説明するように、これにより、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-459">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="a0099-460">最初の応答は、これらのテストの多くで `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="a0099-460">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="a0099-461">一般的なテストでは、`HttpClient` およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="a0099-461">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="a0099-462">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-462">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="a0099-463">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-463">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="a0099-464">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="a0099-464">Make a request for the page.</span></span>
1. <span data-ttu-id="a0099-465">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="a0099-465">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="a0099-466">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="a0099-466">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="a0099-467">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパーメソッド (*Helpers/HtmlHelpers.cs*) では、 [AngleSharp](https://anglesharp.github.io/)パーサーを使用して、による偽造防止チェックを処理します。次のメソッド:</span><span class="sxs-lookup"><span data-stu-id="a0099-467">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="a0099-468">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="a0099-468">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="a0099-469">`GetDocumentAsync` は、元の `HttpResponseMessage`に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-469">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="a0099-470">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-470">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="a0099-471">`HttpClient` の `SendAsync` 拡張メソッドは、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="a0099-471">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="a0099-472">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="a0099-472">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="a0099-473">フォームの [送信] ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="a0099-473">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="a0099-474">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="a0099-474">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="a0099-475">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="a0099-475">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="a0099-476">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="a0099-476">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="a0099-477">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a0099-477">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="a0099-478">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a0099-478">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="a0099-479">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="a0099-479">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="a0099-480">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="a0099-480">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="a0099-481">テストメソッド内で追加の構成が必要な場合、 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は、構成によってさらにカスタマイズされた[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を含む新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="a0099-481">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="a0099-482">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テストメソッドは、`WithWebHostBuilder`の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0099-482">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="a0099-483">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="a0099-483">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="a0099-484">`IndexPageTests` クラス内の別のテストでは、データベース内のすべてのレコードを削除し、`Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0099-484">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="a0099-485">SUT の `messages` フォームの最初の [削除] ボタンを選択すると、SUT に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="a0099-485">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="a0099-486">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="a0099-486">Client options</span></span>

<span data-ttu-id="a0099-487">次の表は、`HttpClient` インスタンスの作成時に使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0099-487">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="a0099-488">オプション</span><span class="sxs-lookup"><span data-stu-id="a0099-488">Option</span></span> | <span data-ttu-id="a0099-489">説明</span><span class="sxs-lookup"><span data-stu-id="a0099-489">Description</span></span> | <span data-ttu-id="a0099-490">[既定値]</span><span class="sxs-lookup"><span data-stu-id="a0099-490">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="a0099-491">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="a0099-491">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="a0099-492">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-492">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="a0099-493">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="a0099-493">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="a0099-494">`HttpClient` インスタンスのベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-494">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="a0099-495">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="a0099-495">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="a0099-496">`HttpClient` インスタンスが cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-496">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="a0099-497">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="a0099-497">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="a0099-498">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-498">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="a0099-499">7</span><span class="sxs-lookup"><span data-stu-id="a0099-499">7</span></span> |

<span data-ttu-id="a0099-500">`WebApplicationFactoryClientOptions` クラスを作成し、 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="a0099-500">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="a0099-501">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="a0099-501">Inject mock services</span></span>

<span data-ttu-id="a0099-502">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="a0099-502">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="a0099-503">**モックサービスを挿入するには、SUT が `Startup.ConfigureServices` メソッドを持つ `Startup` クラスを持っている必要があります。**</span><span class="sxs-lookup"><span data-stu-id="a0099-503">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="a0099-504">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-504">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="a0099-505">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-505">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="a0099-506">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-506">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="a0099-507">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-507">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="a0099-508">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-508">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="a0099-509">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-509">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="a0099-510">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-510">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="a0099-511">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-511">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="a0099-512">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-512">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="a0099-513">モックサービスは、アプリの `QuoteService` を、`TestQuoteService`と呼ばれるテストアプリによって提供されるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0099-513">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="a0099-514">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0099-514">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="a0099-515">`ConfigureTestServices` が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-515">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="a0099-516">テストの実行中に生成されたマークアップには `TestQuoteService`によって指定された引用符が反映されるため、アサーションは次のように渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-516">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="a0099-517">モック認証</span><span class="sxs-lookup"><span data-stu-id="a0099-517">Mock authentication</span></span>

<span data-ttu-id="a0099-518">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a0099-518">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="a0099-519">認証されていないユーザーをアプリのログインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="a0099-519">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="a0099-520">認証されたユーザーのコンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="a0099-520">Returns content for an authenticated user.</span></span>

<span data-ttu-id="a0099-521">SUT では、`/SecurePage` ページは、 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-521">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="a0099-522">詳細については、「[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-522">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="a0099-523">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を `false`に設定してリダイレクトを許可しないように[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)が設定されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-523">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="a0099-524">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-524">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="a0099-525">SUT によって返される状態コードは、予期された[HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="a0099-525">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="a0099-526">応答ヘッダーの `Location` ヘッダー値は、最後のログインページ応答ではなく `http://localhost/Identity/Account/Login`で開始され、`Location` ヘッダーが存在しないことを確認するためにチェックされます。</span><span class="sxs-lookup"><span data-stu-id="a0099-526">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="a0099-527">テストアプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0099-527">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="a0099-528">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-528">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="a0099-529">`TestAuthHandler` は、`AddAuthentication` が `ConfigureTestServices`に登録されている `Test` に認証スキームが設定されている場合に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-529">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="a0099-530">`WebApplicationFactoryClientOptions`の詳細については、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-530">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="a0099-531">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="a0099-531">Set the environment</span></span>

<span data-ttu-id="a0099-532">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-532">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="a0099-533">SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0099-533">To override the SUT's environment:</span></span>

* <span data-ttu-id="a0099-534">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing`などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-534">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="a0099-535">テストアプリの `CreateHostBuilder` をオーバーライドして、`ASPNETCORE`で始まる環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="a0099-535">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="a0099-536">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="a0099-536">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="a0099-537">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリ `System.Reflection.Assembly.FullName`と同じキーを持つ統合テストを含むアセンブリの[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)を検索することによって、アプリの[コンテンツルート](xref:fundamentals/index#content-root)パスを推論します。</span><span class="sxs-lookup"><span data-stu-id="a0099-537">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="a0099-538">正しいキーを持つ属性が見つからない場合 `WebApplicationFactory`、ソリューションファイル ( *.sln*) の検索にフォールバックし、`TEntryPoint` アセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="a0099-538">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="a0099-539">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-539">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="a0099-540">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="a0099-540">Disable shadow copying</span></span>

<span data-ttu-id="a0099-541">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-541">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="a0099-542">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0099-542">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="a0099-543">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xUnit を使用し、正しい構成設定を含む  *xunit.runner.json*ファイルを含めることによって xUnit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="a0099-543">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="a0099-544">詳細については、「[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-544">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="a0099-545">次のコンテンツを使用して、テストプロジェクトのルートに*xunit.runner.json*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="a0099-545">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="a0099-546">Visual Studio を使用している場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピー]** する に設定します。</span><span class="sxs-lookup"><span data-stu-id="a0099-546">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="a0099-547">Visual Studio を使用していない場合は、テストアプリのプロジェクトファイルに `Content` ターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="a0099-547">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="a0099-548">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="a0099-548">Disposal of objects</span></span>

<span data-ttu-id="a0099-549">`IClassFixture` 実装のテストが実行された後、xUnit が[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[HttpClient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-549">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="a0099-550">開発者によってインスタンス化されたオブジェクトが破棄を必要とする場合は、`IClassFixture` の実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="a0099-550">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="a0099-551">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0099-551">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="a0099-552">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="a0099-552">Integration tests sample</span></span>

<span data-ttu-id="a0099-553">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-553">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="a0099-554">アプリ</span><span class="sxs-lookup"><span data-stu-id="a0099-554">App</span></span> | <span data-ttu-id="a0099-555">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="a0099-555">Project directory</span></span> | <span data-ttu-id="a0099-556">説明</span><span class="sxs-lookup"><span data-stu-id="a0099-556">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="a0099-557">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="a0099-557">Message app (the SUT)</span></span> | <span data-ttu-id="a0099-558">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="a0099-558">*src/RazorPagesProject*</span></span> | <span data-ttu-id="a0099-559">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="a0099-559">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="a0099-560">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="a0099-560">Test app</span></span> | <span data-ttu-id="a0099-561">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="a0099-561">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="a0099-562">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-562">Used to integration test the SUT.</span></span> |

<span data-ttu-id="a0099-563">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="a0099-563">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="a0099-564">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a0099-564">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="a0099-565">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="a0099-565">Message app (SUT) organization</span></span>

<span data-ttu-id="a0099-566">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="a0099-566">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="a0099-567">アプリのインデックスページ (*Pages/Index.cshtml*と*Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="a0099-567">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="a0099-568">メッセージは、`Id` (キー) と `Text` (message) の2つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-568">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="a0099-569">`Text` プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="a0099-569">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="a0099-570">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-570">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="a0099-571">アプリには、データベースコンテキストクラス (Data/AppDbContext.cs) にデータアクセス層 (DAL) が含まれています。これは、`AppDbContext` (*Data/AppDbContext.cs*) です。</span><span class="sxs-lookup"><span data-stu-id="a0099-571">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="a0099-572">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-572">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="a0099-573">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-573">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="a0099-574">&#8224;EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="a0099-574">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="a0099-575">このトピックでは、 [xUnit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0099-575">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="a0099-576">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="a0099-576">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="a0099-577">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a0099-577">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="a0099-578">詳細については、「[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)」と「[コントローラーロジックのテスト](/aspnet/core/mvc/controllers/testing)」を参照してください(例では、リポジトリパターンを実装します)」。</span><span class="sxs-lookup"><span data-stu-id="a0099-578">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="a0099-579">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="a0099-579">Test app organization</span></span>

<span data-ttu-id="a0099-580">テストアプリは、 *tests/RazorPagesProject.Tests*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="a0099-580">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="a0099-581">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="a0099-581">Test app directory</span></span> | <span data-ttu-id="a0099-582">説明</span><span class="sxs-lookup"><span data-stu-id="a0099-582">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="a0099-583">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="a0099-583">*AuthTests*</span></span> | <span data-ttu-id="a0099-584">次のテストメソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0099-584">Contains test methods for:</span></span><ul><li><span data-ttu-id="a0099-585">認証されていないユーザーによるセキュリティで保護されたページへのアクセス。</span><span class="sxs-lookup"><span data-stu-id="a0099-585">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="a0099-586">認証されたユーザーがモック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>を使用してセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="a0099-586">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="a0099-587">GitHub ユーザープロファイルを取得し、プロファイルのユーザーログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="a0099-587">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="a0099-588">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="a0099-588">*BasicTests*</span></span> | <span data-ttu-id="a0099-589">ルーティングおよびコンテンツタイプのテストメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-589">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="a0099-590">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="a0099-590">*IntegrationTests*</span></span> | <span data-ttu-id="a0099-591">カスタム `WebApplicationFactory` クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-591">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="a0099-592">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="a0099-592">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="a0099-593">*Utilities.cs*には、テストデータを使用してデータベースをシード処理するために使用する `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0099-593">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="a0099-594">*HtmlHelpers.cs*は、テストメソッドで使用するために AngleSharp `IHtmlDocument` を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="a0099-594">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="a0099-595">*HttpClientExtensions.cs*は、`SendAsync` のオーバーロードを使用して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="a0099-595">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="a0099-596">テストフレームワークは[xUnit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="a0099-596">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="a0099-597">統合テストは、 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0099-597">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="a0099-598">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、`TestHost` および `TestServer` パッケージはテストアプリのプロジェクトファイルまたはテストの開発者構成で直接パッケージ参照を必要としません。アプリケーション.</span><span class="sxs-lookup"><span data-stu-id="a0099-598">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="a0099-599">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="a0099-599">**Seeding the database for testing**</span></span>

<span data-ttu-id="a0099-600">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-600">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="a0099-601">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0099-601">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="a0099-602">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="a0099-602">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a0099-603">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a0099-603">Additional resources</span></span>

* [<span data-ttu-id="a0099-604">単体テスト</span><span class="sxs-lookup"><span data-stu-id="a0099-604">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
