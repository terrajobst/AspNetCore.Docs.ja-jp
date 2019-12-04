---
title: ASP.NET Core での統合テスト
author: guardrex
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/28/2019
uid: test/integration-tests
ms.openlocfilehash: 33f3e29bc649fa65efdff0c47e54a83662005577
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143363"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="9a545-103">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="9a545-104">[Luke Latham](https://github.com/guardrex)、 [Javier Calvarro Jeannine](https://github.com/javiercn)、[上田 Smith](https://ardalis.com/)、 [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="9a545-104">By [Luke Latham](https://github.com/guardrex), [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9a545-105">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="9a545-106">ASP.NET Core では、単体テストフレームワークとテスト Web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9a545-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="9a545-107">このトピックでは、単体テストの基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9a545-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="9a545-108">テストの概念を理解していない場合は、 [.NET Core の単体テストと .NET Standard](/dotnet/core/testing/) に関するトピックとそのリンク先を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="9a545-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9a545-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9a545-110">このサンプルアプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9a545-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="9a545-111">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="9a545-112">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="9a545-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="9a545-113">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="9a545-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="9a545-114">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="9a545-115">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9a545-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="9a545-116">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="9a545-116">Introduction to integration tests</span></span>

<span data-ttu-id="9a545-117">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="9a545-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="9a545-118">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="9a545-119">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="9a545-120">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="9a545-121">データベース</span><span class="sxs-lookup"><span data-stu-id="9a545-121">Database</span></span>
* <span data-ttu-id="9a545-122">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="9a545-122">File system</span></span>
* <span data-ttu-id="9a545-123">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="9a545-123">Network appliances</span></span>
* <span data-ttu-id="9a545-124">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="9a545-124">Request-response pipeline</span></span>

<span data-ttu-id="9a545-125">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="9a545-126">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9a545-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="9a545-127">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="9a545-128">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-128">Require more code and data processing.</span></span>
* <span data-ttu-id="9a545-129">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="9a545-129">Take longer to run.</span></span>

<span data-ttu-id="9a545-130">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="9a545-131">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="9a545-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="9a545-132">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="9a545-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="9a545-133">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="9a545-134">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="9a545-135">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="9a545-136">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="9a545-137">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="9a545-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="9a545-138">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="9a545-139">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="9a545-140">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="9a545-141">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="9a545-142">テストプロジェクトでは、SUT のテスト Web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="9a545-143">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="9a545-144">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="9a545-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="9a545-145">SUT の Web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="9a545-146">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="9a545-147">テストの*配置*ステップが実行されます。テストアプリは要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="9a545-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="9a545-148">*Act*テストステップが実行されます。クライアントが要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="9a545-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="9a545-149">*アサート*テストステップが実行されます。*実際*の応答は*成功*として検証されるか、*予期される*応答に基づいて*失敗*します。</span><span class="sxs-lookup"><span data-stu-id="9a545-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="9a545-150">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="9a545-151">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-151">The test results are reported.</span></span>

<span data-ttu-id="9a545-152">通常、テスト Web ホストは、テストの実行のためにアプリの通常の Web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="9a545-153">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="9a545-154">テスト Web ホストやメモリ内テストサーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="9a545-155">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="9a545-156">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="9a545-157">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="9a545-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="9a545-158">テストの実行時に静的ファイルとページ/ビューが検出されるように、[コンテンツルート](xref:fundamentals/index#content-root)を SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="9a545-159">`TestServer`での SUT のブートストラップ化を効率化する[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a545-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="9a545-160">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="9a545-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="9a545-161">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="9a545-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="9a545-162">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="9a545-163">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a545-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="9a545-164">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="9a545-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="9a545-165">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="9a545-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="9a545-166">Razor Pages アプリでは、通常、ページエンドポイントのテストはページモデルクラスの後に名前が付けられます (たとえば、インデックスページのコンポーネントの統合をテストするには、`IndexPageTests` になります)。</span><span class="sxs-lookup"><span data-stu-id="9a545-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="9a545-167">MVC アプリでは、テストは通常、コントローラークラス別に編成され、テスト対象のコントローラーの後に名前が付けられます (たとえば、Home コントローラーのコンポーネントの統合をテストするには、`HomeControllerTests`)。</span><span class="sxs-lookup"><span data-stu-id="9a545-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="9a545-168">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="9a545-168">Test app prerequisites</span></span>

<span data-ttu-id="9a545-169">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-169">The test project must:</span></span>

* <span data-ttu-id="9a545-170">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="9a545-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="9a545-171">プロジェクトファイルで Web SDK を指定します (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="9a545-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="9a545-172">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="9a545-173">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="9a545-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="9a545-174">サンプルアプリでは、 [Xunit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="9a545-175">xunit</span><span class="sxs-lookup"><span data-stu-id="9a545-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="9a545-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="9a545-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="9a545-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="9a545-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="9a545-178">テストでも Entity Framework Core が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="9a545-179">アプリの参照:</span><span class="sxs-lookup"><span data-stu-id="9a545-179">The app references:</span></span>

* [<span data-ttu-id="9a545-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9a545-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="9a545-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9a545-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="9a545-182">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9a545-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="9a545-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="9a545-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="9a545-184">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="9a545-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="9a545-185">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="9a545-185">SUT environment</span></span>

<span data-ttu-id="9a545-186">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="9a545-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="9a545-187">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="9a545-188">[Webapplicationfactory \<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)は、統合テスト用の[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="9a545-189">`TEntryPoint` は、SUT のエントリポイントクラス (通常は `Startup` クラス) です。</span><span class="sxs-lookup"><span data-stu-id="9a545-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="9a545-190">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a545-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="9a545-191">次のテストクラス `BasicTests`では、`WebApplicationFactory` を使用して、SUT をブートストラップし、 [HttpClient](/dotnet/api/system.net.http.httpclient)をテストメソッドに `Get_EndpointsReturnSuccessAndCorrectContentType`します。</span><span class="sxs-lookup"><span data-stu-id="9a545-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="9a545-192">メソッドは、応答ステータスコードが成功したかどうかを確認し (200-299 の範囲の状態コード)、`Content-Type` ヘッダーは複数のアプリページで `text/html; charset=utf-8` ます。</span><span class="sxs-lookup"><span data-stu-id="9a545-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="9a545-193">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)が `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトを追跡し、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="9a545-194">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="9a545-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="9a545-195">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="9a545-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="9a545-196">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="9a545-197">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="9a545-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="9a545-198">Web ホストの構成は、`WebApplicationFactory` から継承して1つ以上のカスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="9a545-199">`WebApplicationFactory` から継承し、 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9a545-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="9a545-200">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="9a545-201">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-201">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="9a545-202">この方法については、「[統合テストのサンプル: テストアプリの組織](#test-app-organization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="9a545-203">SUT のデータベースコンテキストは `Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a545-204">テストアプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行され*た後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="9a545-205">アプリケーションのデータベースとは異なるデータベースをテストに使用するには、アプリのデータベースコンテキストを `builder.ConfigureServices` で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-205">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="9a545-206">サンプルアプリでは、データベースコンテキストのサービス記述子を検索し、記述子を使用してサービス登録を削除します。</span><span class="sxs-lookup"><span data-stu-id="9a545-206">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="9a545-207">次に、ファクトリは、テストにメモリ内データベースを使用する新しい `ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="9a545-207">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="9a545-208">インメモリデータベースとは別のデータベースに接続するには、`UseInMemoryDatabase` 呼び出しを変更して、コンテキストを別のデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="9a545-208">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="9a545-209">SQL Server テストデータベースを使用するには:</span><span class="sxs-lookup"><span data-stu-id="9a545-209">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="9a545-210">プロジェクトファイルで、 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="9a545-210">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="9a545-211">データベースへの接続文字列を使用して `UseSqlServer` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9a545-211">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="9a545-212">テストクラスでは、カスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-212">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="9a545-213">次の例では、`IndexPageTests` クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-213">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="9a545-214">サンプルアプリのクライアントは、`HttpClient` がリダイレクトされるのを防ぐように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-214">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="9a545-215">「[モック認証](#mock-authentication)」セクションで後ほど説明するように、これにより、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-215">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="9a545-216">最初の応答は、これらのテストの多くで `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="9a545-216">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="9a545-217">一般的なテストでは、`HttpClient` およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-217">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="9a545-218">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-218">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="9a545-219">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-219">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="9a545-220">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a545-220">Make a request for the page.</span></span>
1. <span data-ttu-id="9a545-221">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="9a545-221">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="9a545-222">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a545-222">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="9a545-223">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパーメソッド (*Helpers/HtmlHelpers.cs*) では、 [AngleSharp](https://anglesharp.github.io/)パーサーを使用して、による偽造防止チェックを処理します。次のメソッド:</span><span class="sxs-lookup"><span data-stu-id="9a545-223">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="9a545-224">`GetDocumentAsync` &ndash; は[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="9a545-224">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="9a545-225">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-225">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="9a545-226">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-226">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="9a545-227">`HttpClient` の `SendAsync` 拡張メソッドは、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="9a545-227">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="9a545-228">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9a545-228">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="9a545-229">フォームの [送信] ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="9a545-229">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="9a545-230">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a545-230">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="9a545-231">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a545-231">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="9a545-232">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="9a545-232">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="9a545-233">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9a545-233">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="9a545-234">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a545-234">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="9a545-235">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="9a545-235">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="9a545-236">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="9a545-236">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="9a545-237">テストメソッド内で追加の構成が必要な場合、 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は、構成によってさらにカスタマイズされた[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を含む新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="9a545-237">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="9a545-238">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テストメソッドは、`WithWebHostBuilder`の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9a545-238">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="9a545-239">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="9a545-239">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="9a545-240">`IndexPageTests` クラス内の別のテストでは、データベース内のすべてのレコードを削除し、`Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-240">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="9a545-241">SUT の `messages` フォームの最初の [削除] ボタンを選択すると、SUT に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="9a545-241">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="9a545-242">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="9a545-242">Client options</span></span>

<span data-ttu-id="9a545-243">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)を示します。</span><span class="sxs-lookup"><span data-stu-id="9a545-243">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="9a545-244">オプション</span><span class="sxs-lookup"><span data-stu-id="9a545-244">Option</span></span> | <span data-ttu-id="9a545-245">説明</span><span class="sxs-lookup"><span data-stu-id="9a545-245">Description</span></span> | <span data-ttu-id="9a545-246">既定</span><span class="sxs-lookup"><span data-stu-id="9a545-246">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="9a545-247">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="9a545-247">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="9a545-248">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-248">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="9a545-249">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="9a545-249">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="9a545-250">`HttpClient` インスタンスのベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-250">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="9a545-251">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="9a545-251">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="9a545-252">`HttpClient` インスタンスが cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-252">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="9a545-253">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="9a545-253">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="9a545-254">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-254">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="9a545-255">7</span><span class="sxs-lookup"><span data-stu-id="9a545-255">7</span></span> |

<span data-ttu-id="9a545-256">`WebApplicationFactoryClientOptions` クラスを作成し、 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="9a545-256">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="9a545-257">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="9a545-257">Inject mock services</span></span>

<span data-ttu-id="9a545-258">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="9a545-258">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="9a545-259">**モックサービスを挿入するには、SUT が `Startup.ConfigureServices` メソッドを持つ `Startup` クラスを持っている必要があります。**</span><span class="sxs-lookup"><span data-stu-id="9a545-259">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="9a545-260">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-260">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="9a545-261">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-261">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="9a545-262">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-262">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="9a545-263">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-263">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="9a545-264">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-264">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="9a545-265">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-265">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="9a545-266">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-266">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="9a545-267">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-267">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="9a545-268">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-268">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="9a545-269">モックサービスは、アプリの `QuoteService` を、`TestQuoteService` というテストアプリによって提供されるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9a545-269">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="9a545-270">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-270">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="9a545-271">`ConfigureTestServices` が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-271">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="9a545-272">テストの実行中に生成されたマークアップには `TestQuoteService` によって指定された見積もりテキストが反映されるため、アサーションは次のように渡されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-272">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="9a545-273">モック認証</span><span class="sxs-lookup"><span data-stu-id="9a545-273">Mock authentication</span></span>

<span data-ttu-id="9a545-274">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-274">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="9a545-275">認証されていないユーザーをアプリのログインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="9a545-275">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="9a545-276">認証されたユーザーのコンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="9a545-276">Returns content for an authenticated user.</span></span>

<span data-ttu-id="9a545-277">SUT では、`/SecurePage` ページは、 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-277">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="9a545-278">詳細については、「 [Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-278">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="9a545-279">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を `false`に設定してリダイレクトを許可しないように[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)が設定されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-279">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="9a545-280">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-280">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="9a545-281">SUT によって返される状態コードは、予期された[HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="9a545-281">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="9a545-282">応答ヘッダーの `Location` ヘッダー値は、最後のログインページ応答ではなく `http://localhost/Identity/Account/Login` で開始されていることを確認し、`Location` ヘッダーが存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-282">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="9a545-283">テストアプリでは、認証と承認の側面をテストするために[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-283">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="9a545-284">最小のシナリオでは、AuthenticateResult が返さ[れます。成功](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="9a545-284">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="9a545-285">`TestAuthHandler` は、`AddAuthentication` が `ConfigureTestServices`に登録されている `Test` に認証スキームが設定されている場合に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-285">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="9a545-286">`WebApplicationFactoryClientOptions`の詳細については、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-286">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="9a545-287">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="9a545-287">Set the environment</span></span>

<span data-ttu-id="9a545-288">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-288">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="9a545-289">SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9a545-289">To override the SUT's environment:</span></span>

* <span data-ttu-id="9a545-290">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing`などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-290">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="9a545-291">テストアプリの `CreateHostBuilder` をオーバーライドして、`ASPNETCORE`で始まる環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9a545-291">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="9a545-292">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="9a545-292">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="9a545-293">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリ `System.Reflection.Assembly.FullName`と同じキーを持つ統合テストを含むアセンブリの[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)を検索することによって、アプリの[コンテンツルート](xref:fundamentals/index#content-root)パスを推論します。</span><span class="sxs-lookup"><span data-stu-id="9a545-293">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="9a545-294">正しいキーを持つ属性が見つからない場合 `WebApplicationFactory`、ソリューションファイル ( *.sln*) の検索にフォールバックし、`TEntryPoint` アセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9a545-294">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="9a545-295">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-295">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="9a545-296">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="9a545-296">Disable shadow copying</span></span>

<span data-ttu-id="9a545-297">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-297">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="9a545-298">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-298">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="9a545-299">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xUnit を使用し、正しい構成設定を含む  *xunit.runner.json*ファイルを含めることによって xUnit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="9a545-299">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="9a545-300">詳細については、「 [JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-300">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="9a545-301">次のコンテンツを使用して、テストプロジェクトのルートに*xunit.runner.json*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="9a545-301">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="9a545-302">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="9a545-302">Disposal of objects</span></span>

<span data-ttu-id="9a545-303">`IClassFixture` 実装のテストが実行された後、xUnit が[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[HttpClient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-303">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="9a545-304">開発者によってインスタンス化されたオブジェクトが破棄を必要とする場合は、`IClassFixture` の実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="9a545-304">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="9a545-305">詳細については、「 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-305">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="9a545-306">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="9a545-306">Integration tests sample</span></span>

<span data-ttu-id="9a545-307">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-307">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="9a545-308">アプリ</span><span class="sxs-lookup"><span data-stu-id="9a545-308">App</span></span> | <span data-ttu-id="9a545-309">プロジェクトディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a545-309">Project directory</span></span> | <span data-ttu-id="9a545-310">説明</span><span class="sxs-lookup"><span data-stu-id="9a545-310">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="9a545-311">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="9a545-311">Message app (the SUT)</span></span> | <span data-ttu-id="9a545-312">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="9a545-312">*src/RazorPagesProject*</span></span> | <span data-ttu-id="9a545-313">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="9a545-313">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="9a545-314">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="9a545-314">Test app</span></span> | <span data-ttu-id="9a545-315">*テスト/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="9a545-315">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="9a545-316">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-316">Used to integration test the SUT.</span></span> |

<span data-ttu-id="9a545-317">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-317">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="9a545-318">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9a545-318">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="9a545-319">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="9a545-319">Message app (SUT) organization</span></span>

<span data-ttu-id="9a545-320">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="9a545-320">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="9a545-321">アプリのインデックスページ (*Pages/Index.cshtml*と*Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="9a545-321">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="9a545-322">メッセージは、`Id` (キー) と `Text` (message) の2つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-322">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="9a545-323">`Text` プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-323">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="9a545-324">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-324">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="9a545-325">アプリには、データベースコンテキストクラス (Data/AppDbContext.cs) にデータアクセス層 (DAL) が含まれています。これは、`AppDbContext` (*Data/AppDbContext.cs*) です。</span><span class="sxs-lookup"><span data-stu-id="9a545-325">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="9a545-326">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-326">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="9a545-327">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-327">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="9a545-328">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="9a545-328">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="9a545-329">このトピックでは、 [xUnit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-329">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="9a545-330">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="9a545-330">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="9a545-331">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9a545-331">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="9a545-332">詳細については、「[インフラストラクチャの永続化層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テストコントローラーのロジック](/aspnet/core/mvc/controllers/testing)の設計 (例では、リポジトリパターンを実装します)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-332">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="9a545-333">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="9a545-333">Test app organization</span></span>

<span data-ttu-id="9a545-334">テストアプリは、 *tests/RazorPagesProject.Tests*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="9a545-334">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="9a545-335">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a545-335">Test app directory</span></span> | <span data-ttu-id="9a545-336">説明</span><span class="sxs-lookup"><span data-stu-id="9a545-336">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="9a545-337">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="9a545-337">*AuthTests*</span></span> | <span data-ttu-id="9a545-338">次のテストメソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-338">Contains test methods for:</span></span><ul><li><span data-ttu-id="9a545-339">認証されていないユーザーによるセキュリティで保護されたページへのアクセス。</span><span class="sxs-lookup"><span data-stu-id="9a545-339">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="9a545-340">認証されたユーザーがモック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>を使用してセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="9a545-340">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="9a545-341">GitHub ユーザープロファイルを取得し、プロファイルのユーザーログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="9a545-341">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="9a545-342">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="9a545-342">*BasicTests*</span></span> | <span data-ttu-id="9a545-343">ルーティングおよびコンテンツタイプのテストメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-343">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="9a545-344">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="9a545-344">*IntegrationTests*</span></span> | <span data-ttu-id="9a545-345">カスタム `WebApplicationFactory` クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-345">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="9a545-346">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="9a545-346">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="9a545-347">*Utilities.cs*には、テストデータを使用してデータベースをシード処理するために使用される `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-347">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="9a545-348">*HtmlHelpers.cs*は、テストメソッドで使用するために AngleSharp `IHtmlDocument` を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a545-348">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="9a545-349">*HttpClientExtensions.cs*は、`SendAsync` のオーバーロードを使用して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="9a545-349">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="9a545-350">テストフレームワークは[xUnit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="9a545-350">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="9a545-351">統合テストは、 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-351">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="9a545-352">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、`TestHost` および `TestServer` パッケージはテストアプリのプロジェクトファイルまたはテストの開発者構成で直接パッケージ参照を必要としません。アプリケーション.</span><span class="sxs-lookup"><span data-stu-id="9a545-352">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="9a545-353">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="9a545-353">**Seeding the database for testing**</span></span>

<span data-ttu-id="9a545-354">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-354">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="9a545-355">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-355">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="9a545-356">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="9a545-356">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="9a545-357">SUT のデータベースコンテキストは `Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-357">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a545-358">テストアプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行され*た後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-358">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="9a545-359">テストに別のデータベースを使用するには、アプリのデータベースコンテキストを `builder.ConfigureServices` で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-359">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="9a545-360">詳細については、「 [WebApplicationFactory のカスタマイズ](#customize-webapplicationfactory)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-360">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a545-361">統合テストでは、アプリケーションのコンポーネントが、データベース、ファイルシステム、ネットワークなど、アプリのサポートインフラストラクチャを含むレベルで正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-361">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="9a545-362">ASP.NET Core では、単体テストフレームワークとテスト Web ホストおよびメモリ内テストサーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9a545-362">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="9a545-363">このトピックでは、単体テストの基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9a545-363">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="9a545-364">テストの概念を理解していない場合は、 [.NET Core の単体テストと .NET Standard](/dotnet/core/testing/) に関するトピックとそのリンク先を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-364">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="9a545-365">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9a545-365">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9a545-366">このサンプルアプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9a545-366">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="9a545-367">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-367">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="9a545-368">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="9a545-368">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="9a545-369">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="9a545-369">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="9a545-370">Razor ページの単体テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-370">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="9a545-371">SPAs をテストする場合は、ブラウザーを自動化できる[Selenium](https://www.seleniumhq.org/)などのツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9a545-371">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="9a545-372">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="9a545-372">Introduction to integration tests</span></span>

<span data-ttu-id="9a545-373">統合テストでは、[単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="9a545-373">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="9a545-374">単体テストは、個々のクラスメソッドなど、分離されたソフトウェアコンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-374">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="9a545-375">統合テストでは、2つ以上のアプリコンポーネントが連携して、予想される結果を生成することを確認します。要求を完全に処理するために必要なすべてのコンポーネントが含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-375">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="9a545-376">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-376">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="9a545-377">データベース</span><span class="sxs-lookup"><span data-stu-id="9a545-377">Database</span></span>
* <span data-ttu-id="9a545-378">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="9a545-378">File system</span></span>
* <span data-ttu-id="9a545-379">ネットワークアプライアンス</span><span class="sxs-lookup"><span data-stu-id="9a545-379">Network appliances</span></span>
* <span data-ttu-id="9a545-380">要求-応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="9a545-380">Request-response pipeline</span></span>

<span data-ttu-id="9a545-381">単体テストでは、インフラストラクチャコンポーネントの代わりに、*フェイク*または*モックオブジェクト*と呼ばれる作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-381">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="9a545-382">単体テストとは対照的に、統合テストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9a545-382">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="9a545-383">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-383">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="9a545-384">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-384">Require more code and data processing.</span></span>
* <span data-ttu-id="9a545-385">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="9a545-385">Take longer to run.</span></span>

<span data-ttu-id="9a545-386">そのため、統合テストの使用を最も重要なインフラストラクチャシナリオに限定してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-386">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="9a545-387">単体テストまたは統合テストを使用して動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="9a545-387">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="9a545-388">データベースとファイルシステムを使用して、データおよびファイルアクセスのすべての順列について、統合テストを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="9a545-388">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="9a545-389">アプリ全体でデータベースとファイルシステムを操作する場所に関係なく、読み取り、書き込み、更新、削除の各統合テストのセットは、通常、データベースおよびファイルシステムコンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-389">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="9a545-390">これらのコンポーネントと対話するメソッドロジックのルーチンテストでは、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-390">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="9a545-391">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-391">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="9a545-392">統合テストの説明では、テスト対象のプロジェクトは、多くの場合、*テスト対象のシステム*、または "SUT" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-392">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="9a545-393">*テストされた ASP.NET Core アプリを参照するには、このトピック全体で "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="9a545-393">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="9a545-394">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-394">ASP.NET Core integration tests</span></span>

<span data-ttu-id="9a545-395">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-395">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="9a545-396">テストプロジェクトは、テストを格納して実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-396">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="9a545-397">テストプロジェクトには、SUT への参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-397">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="9a545-398">テストプロジェクトでは、SUT のテスト Web ホストを作成し、テストサーバークライアントを使用して、SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-398">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="9a545-399">テストランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-399">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="9a545-400">統合テストは、通常の*配置*、*操作*、および*アサート*の各テストステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="9a545-400">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="9a545-401">SUT の Web ホストが構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-401">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="9a545-402">アプリケーションに要求を送信するためのテストサーバークライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-402">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="9a545-403">テストの*配置*ステップが実行されます。テストアプリは要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="9a545-403">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="9a545-404">*Act*テストステップが実行されます。クライアントが要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="9a545-404">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="9a545-405">*アサート*テストステップが実行されます。*実際*の応答は*成功*として検証されるか、*予期される*応答に基づいて*失敗*します。</span><span class="sxs-lookup"><span data-stu-id="9a545-405">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="9a545-406">すべてのテストが実行されるまで、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-406">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="9a545-407">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-407">The test results are reported.</span></span>

<span data-ttu-id="9a545-408">通常、テスト Web ホストは、テストの実行のためにアプリの通常の Web ホストとは異なる方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-408">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="9a545-409">たとえば、テストには別のデータベースまたは異なるアプリ設定が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-409">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="9a545-410">テスト Web ホストやメモリ内テストサーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャコンポーネントは、 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-410">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="9a545-411">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-411">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="9a545-412">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-412">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="9a545-413">依存関係ファイル (*deps*) を SUT からテストプロジェクトの*bin*ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="9a545-413">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="9a545-414">テストの実行時に静的ファイルとページ/ビューが検出されるように、[コンテンツルート](xref:fundamentals/index#content-root)を SUT のプロジェクトルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-414">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="9a545-415">`TestServer`での SUT のブートストラップ化を効率化する[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)クラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a545-415">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="9a545-416">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)のドキュメントでは、テストプロジェクトとテストランナーを設定する方法と、テストを実行する方法の詳細な手順と、テストおよびテストクラスの名前付け方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="9a545-416">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="9a545-417">アプリのテストプロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="9a545-417">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="9a545-418">これにより、インフラストラクチャテストコンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-418">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="9a545-419">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a545-419">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="9a545-420">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いはありません。</span><span class="sxs-lookup"><span data-stu-id="9a545-420">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="9a545-421">唯一の違いは、テストの名前付け方法です。</span><span class="sxs-lookup"><span data-stu-id="9a545-421">The only difference is in how the tests are named.</span></span> <span data-ttu-id="9a545-422">Razor Pages アプリでは、通常、ページエンドポイントのテストはページモデルクラスの後に名前が付けられます (たとえば、インデックスページのコンポーネントの統合をテストするには、`IndexPageTests` になります)。</span><span class="sxs-lookup"><span data-stu-id="9a545-422">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="9a545-423">MVC アプリでは、テストは通常、コントローラークラス別に編成され、テスト対象のコントローラーの後に名前が付けられます (たとえば、Home コントローラーのコンポーネントの統合をテストするには、`HomeControllerTests`)。</span><span class="sxs-lookup"><span data-stu-id="9a545-423">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="9a545-424">テストアプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="9a545-424">Test app prerequisites</span></span>

<span data-ttu-id="9a545-425">テストプロジェクトは次の条件を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-425">The test project must:</span></span>

* <span data-ttu-id="9a545-426">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="9a545-426">Reference the following packages:</span></span>
  * [<span data-ttu-id="9a545-427">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="9a545-427">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="9a545-428">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="9a545-428">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="9a545-429">プロジェクトファイルで Web SDK を指定します (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="9a545-429">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="9a545-430">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するときは、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-430">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9a545-431">これらの前提条件は、[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で見ることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-431">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="9a545-432">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="9a545-432">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="9a545-433">サンプルアプリでは、 [Xunit](https://xunit.github.io/)テストフレームワークと[AngleSharp](https://anglesharp.github.io/) parser ライブラリを使用しています。このため、サンプルアプリでも参照できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-433">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="9a545-434">xunit</span><span class="sxs-lookup"><span data-stu-id="9a545-434">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="9a545-435">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="9a545-435">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="9a545-436">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="9a545-436">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="9a545-437">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="9a545-437">SUT environment</span></span>

<span data-ttu-id="9a545-438">SUT の[環境](xref:fundamentals/environments)が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="9a545-438">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="9a545-439">既定の WebApplicationFactory を使用した基本テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-439">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="9a545-440">[Webapplicationfactory \<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)は、統合テスト用の[testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-440">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="9a545-441">`TEntryPoint` は、SUT のエントリポイントクラス (通常は `Startup` クラス) です。</span><span class="sxs-lookup"><span data-stu-id="9a545-441">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="9a545-442">テストクラスは、クラスにテストが含まれていることを示す*クラスフィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内のテスト間で共有オブジェクトインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a545-442">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="9a545-443">次のテストクラス `BasicTests`では、`WebApplicationFactory` を使用して、SUT をブートストラップし、 [HttpClient](/dotnet/api/system.net.http.httpclient)をテストメソッドに `Get_EndpointsReturnSuccessAndCorrectContentType`します。</span><span class="sxs-lookup"><span data-stu-id="9a545-443">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="9a545-444">メソッドは、応答ステータスコードが成功したかどうかを確認し (200-299 の範囲の状態コード)、`Content-Type` ヘッダーは複数のアプリページで `text/html; charset=utf-8` ます。</span><span class="sxs-lookup"><span data-stu-id="9a545-444">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="9a545-445">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)が `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトを追跡し、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-445">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="9a545-446">既定では、 [GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、必須ではない cookie は要求間で保持されません。</span><span class="sxs-lookup"><span data-stu-id="9a545-446">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="9a545-447">TempData プロバイダーで使用されているような、重要ではない cookie を保持するには、テストに必須としてマークします。</span><span class="sxs-lookup"><span data-stu-id="9a545-447">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="9a545-448">Cookie を必須としてマークする手順については、「[必須 cookie](xref:security/gdpr#essential-cookies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-448">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="9a545-449">WebApplicationFactory をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="9a545-449">Customize WebApplicationFactory</span></span>

<span data-ttu-id="9a545-450">Web ホストの構成は、`WebApplicationFactory` から継承して1つ以上のカスタムファクトリを作成することによって、テストクラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-450">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="9a545-451">`WebApplicationFactory` から継承し、 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9a545-451">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="9a545-452">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)では、 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)を使用してサービスコレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-452">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="9a545-453">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-453">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="9a545-454">この方法については、「[統合テストのサンプル: テストアプリの組織](#test-app-organization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-454">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="9a545-455">テストクラスでは、カスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-455">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="9a545-456">次の例では、`IndexPageTests` クラスのファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-456">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="9a545-457">サンプルアプリのクライアントは、`HttpClient` がリダイレクトされるのを防ぐように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-457">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="9a545-458">「[モック認証](#mock-authentication)」セクションで後ほど説明するように、これにより、アプリの最初の応答の結果を確認するテストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-458">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="9a545-459">最初の応答は、これらのテストの多くで `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="9a545-459">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="9a545-460">一般的なテストでは、`HttpClient` およびヘルパーメソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a545-460">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="9a545-461">SUT に対する POST 要求は、アプリの[データ保護のアンチ偽造システム](xref:security/data-protection/introduction)によって自動的に行われるアンチ偽造チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-461">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="9a545-462">テストの POST 要求を配置するには、テストアプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-462">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="9a545-463">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a545-463">Make a request for the page.</span></span>
1. <span data-ttu-id="9a545-464">アンチ偽造 cookie を解析し、応答から検証トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="9a545-464">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="9a545-465">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a545-465">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="9a545-466">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパーメソッド (*Helpers/HtmlHelpers.cs*) では、 [AngleSharp](https://anglesharp.github.io/)パーサーを使用して、による偽造防止チェックを処理します。次のメソッド:</span><span class="sxs-lookup"><span data-stu-id="9a545-466">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="9a545-467">`GetDocumentAsync` &ndash; は[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="9a545-467">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="9a545-468">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-468">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="9a545-469">詳細については、 [AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-469">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="9a545-470">`HttpClient` の `SendAsync` 拡張メソッドは、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を作成し、 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="9a545-470">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="9a545-471">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9a545-471">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="9a545-472">フォームの [送信] ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="9a545-472">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="9a545-473">フォーム値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a545-473">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="9a545-474">送信ボタン (`IHtmlElement`) とフォーム値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a545-474">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="9a545-475">[AngleSharp](https://anglesharp.github.io/)は、このトピックとサンプルアプリのデモンストレーションのために使用されるサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="9a545-475">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="9a545-476">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9a545-476">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="9a545-477">[Html アジリティ Pack (HAP)](https://html-agility-pack.net/)など、その他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a545-477">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="9a545-478">もう1つの方法は、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造 cookie を直接処理することです。</span><span class="sxs-lookup"><span data-stu-id="9a545-478">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="9a545-479">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="9a545-479">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="9a545-480">テストメソッド内で追加の構成が必要な場合、 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)は、構成によってさらにカスタマイズされた[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)を含む新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="9a545-480">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="9a545-481">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テストメソッドは、`WithWebHostBuilder`の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9a545-481">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="9a545-482">このテストでは、SUT でフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="9a545-482">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="9a545-483">`IndexPageTests` クラス内の別のテストでは、データベース内のすべてのレコードを削除し、`Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行できる操作を実行するため、このテストメソッドでデータベースを再シード処理して、SUT が削除するレコードが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-483">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="9a545-484">SUT の `messages` フォームの最初の [削除] ボタンを選択すると、SUT に対する要求でシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="9a545-484">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="9a545-485">クライアントオプション</span><span class="sxs-lookup"><span data-stu-id="9a545-485">Client options</span></span>

<span data-ttu-id="9a545-486">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)を示します。</span><span class="sxs-lookup"><span data-stu-id="9a545-486">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="9a545-487">オプション</span><span class="sxs-lookup"><span data-stu-id="9a545-487">Option</span></span> | <span data-ttu-id="9a545-488">説明</span><span class="sxs-lookup"><span data-stu-id="9a545-488">Description</span></span> | <span data-ttu-id="9a545-489">既定</span><span class="sxs-lookup"><span data-stu-id="9a545-489">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="9a545-490">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="9a545-490">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="9a545-491">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-491">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="9a545-492">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="9a545-492">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="9a545-493">`HttpClient` インスタンスのベースアドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-493">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="9a545-494">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="9a545-494">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="9a545-495">`HttpClient` インスタンスが cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-495">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="9a545-496">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="9a545-496">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="9a545-497">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-497">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="9a545-498">7</span><span class="sxs-lookup"><span data-stu-id="9a545-498">7</span></span> |

<span data-ttu-id="9a545-499">`WebApplicationFactoryClientOptions` クラスを作成し、 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="9a545-499">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="9a545-500">モックサービスの注入</span><span class="sxs-lookup"><span data-stu-id="9a545-500">Inject mock services</span></span>

<span data-ttu-id="9a545-501">サービスは、ホストビルダーで[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)を呼び出すことによって、テストでオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="9a545-501">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="9a545-502">**モックサービスを挿入するには、SUT が `Startup.ConfigureServices` メソッドを持つ `Startup` クラスを持っている必要があります。**</span><span class="sxs-lookup"><span data-stu-id="9a545-502">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="9a545-503">サンプルの SUT には、引用符を返すスコープ付きサービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-503">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="9a545-504">インデックスページが要求されると、インデックスページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-504">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="9a545-505">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-505">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="9a545-506">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-506">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="9a545-507">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-507">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="9a545-508">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-508">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="9a545-509">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-509">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="9a545-510">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-510">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="9a545-511">統合テストでサービスと引用の挿入をテストするために、テストでは、モックサービスが SUT に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-511">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="9a545-512">モックサービスは、アプリの `QuoteService` を、`TestQuoteService` というテストアプリによって提供されるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9a545-512">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="9a545-513">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a545-513">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="9a545-514">`ConfigureTestServices` が呼び出され、スコープサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-514">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="9a545-515">テストの実行中に生成されたマークアップには `TestQuoteService` によって指定された見積もりテキストが反映されるため、アサーションは次のように渡されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-515">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="9a545-516">モック認証</span><span class="sxs-lookup"><span data-stu-id="9a545-516">Mock authentication</span></span>

<span data-ttu-id="9a545-517">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-517">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="9a545-518">認証されていないユーザーをアプリのログインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="9a545-518">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="9a545-519">認証されたユーザーのコンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="9a545-519">Returns content for an authenticated user.</span></span>

<span data-ttu-id="9a545-520">SUT では、`/SecurePage` ページは、 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)規約を使用して、ページに[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を適用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-520">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="9a545-521">詳細については、「 [Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-521">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="9a545-522">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)を `false`に設定してリダイレクトを許可しないように[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)が設定されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-522">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="9a545-523">クライアントがリダイレクトに従うことを禁止することで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-523">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="9a545-524">SUT によって返される状態コードは、予期された[HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)結果と比較して確認できます。これは、ログインページにリダイレクトした後の最終的な状態コードではなく、 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)です。</span><span class="sxs-lookup"><span data-stu-id="9a545-524">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="9a545-525">応答ヘッダーの `Location` ヘッダー値は、最後のログインページ応答ではなく `http://localhost/Identity/Account/Login` で開始されていることを確認し、`Location` ヘッダーが存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a545-525">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="9a545-526">テストアプリでは、認証と承認の側面をテストするために[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a545-526">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="9a545-527">最小のシナリオでは、AuthenticateResult が返さ[れます。成功](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="9a545-527">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="9a545-528">`TestAuthHandler` は、`AddAuthentication` が `ConfigureTestServices`に登録されている `Test` に認証スキームが設定されている場合に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-528">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="9a545-529">`WebApplicationFactoryClientOptions`の詳細については、「[クライアントオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-529">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="9a545-530">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="9a545-530">Set the environment</span></span>

<span data-ttu-id="9a545-531">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-531">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="9a545-532">SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9a545-532">To override the SUT's environment:</span></span>

* <span data-ttu-id="9a545-533">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing`などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-533">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="9a545-534">テストアプリの `CreateHostBuilder` をオーバーライドして、`ASPNETCORE`で始まる環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9a545-534">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="9a545-535">テストインフラストラクチャがアプリコンテンツのルートパスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="9a545-535">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="9a545-536">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリ `System.Reflection.Assembly.FullName`と同じキーを持つ統合テストを含むアセンブリの[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)を検索することによって、アプリの[コンテンツルート](xref:fundamentals/index#content-root)パスを推論します。</span><span class="sxs-lookup"><span data-stu-id="9a545-536">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="9a545-537">正しいキーを持つ属性が見つからない場合 `WebApplicationFactory`、ソリューションファイル ( *.sln*) の検索にフォールバックし、`TEntryPoint` アセンブリ名をソリューションディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9a545-537">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="9a545-538">アプリのルートディレクトリ (コンテンツのルートパス) は、ビューとコンテンツファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-538">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="9a545-539">シャドウコピーを無効にする</span><span class="sxs-lookup"><span data-stu-id="9a545-539">Disable shadow copying</span></span>

<span data-ttu-id="9a545-540">シャドウコピーにより、テストは出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-540">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="9a545-541">テストが適切に機能するには、シャドウコピーを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a545-541">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="9a545-542">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では xUnit を使用し、正しい構成設定を含む  *xunit.runner.json*ファイルを含めることによって xUnit のシャドウコピーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="9a545-542">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="9a545-543">詳細については、「 [JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-543">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="9a545-544">次のコンテンツを使用して、テストプロジェクトのルートに*xunit.runner.json*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="9a545-544">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="9a545-545">Visual Studio を使用している場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピー]** する に設定します。</span><span class="sxs-lookup"><span data-stu-id="9a545-545">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="9a545-546">Visual Studio を使用していない場合は、テストアプリのプロジェクトファイルに `Content` ターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="9a545-546">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="9a545-547">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="9a545-547">Disposal of objects</span></span>

<span data-ttu-id="9a545-548">`IClassFixture` 実装のテストが実行された後、xUnit が[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を破棄すると[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[HttpClient](/dotnet/api/system.net.http.httpclient)が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-548">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="9a545-549">開発者によってインスタンス化されたオブジェクトが破棄を必要とする場合は、`IClassFixture` の実装でそれらを破棄します。</span><span class="sxs-lookup"><span data-stu-id="9a545-549">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="9a545-550">詳細については、「 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-550">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="9a545-551">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="9a545-551">Integration tests sample</span></span>

<span data-ttu-id="9a545-552">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の2つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-552">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="9a545-553">アプリ</span><span class="sxs-lookup"><span data-stu-id="9a545-553">App</span></span> | <span data-ttu-id="9a545-554">プロジェクトディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a545-554">Project directory</span></span> | <span data-ttu-id="9a545-555">説明</span><span class="sxs-lookup"><span data-stu-id="9a545-555">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="9a545-556">メッセージアプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="9a545-556">Message app (the SUT)</span></span> | <span data-ttu-id="9a545-557">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="9a545-557">*src/RazorPagesProject*</span></span> | <span data-ttu-id="9a545-558">ユーザーがメッセージを追加、削除、削除、および分析することを許可します。</span><span class="sxs-lookup"><span data-stu-id="9a545-558">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="9a545-559">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="9a545-559">Test app</span></span> | <span data-ttu-id="9a545-560">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="9a545-560">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="9a545-561">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-561">Used to integration test the SUT.</span></span> |

<span data-ttu-id="9a545-562">テストは、 [Visual Studio](https://visualstudio.microsoft.com)などの IDE の組み込みのテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="9a545-562">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="9a545-563">[Visual Studio Code](https://code.visualstudio.com/)またはコマンドラインを使用している場合は、*テスト/RazorPagesProject*ディレクトリのコマンドプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9a545-563">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="9a545-564">メッセージアプリ (SUT) 組織</span><span class="sxs-lookup"><span data-stu-id="9a545-564">Message app (SUT) organization</span></span>

<span data-ttu-id="9a545-565">SUT は、次の特性を持つ Razor Pages メッセージシステムです。</span><span class="sxs-lookup"><span data-stu-id="9a545-565">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="9a545-566">アプリのインデックスページ (*Pages/Index.cshtml*と*Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析を制御する UI およびページモデルのメソッドが用意されています (メッセージごとの平均語句)。</span><span class="sxs-lookup"><span data-stu-id="9a545-566">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="9a545-567">メッセージは、`Id` (キー) と `Text` (message) の2つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-567">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="9a545-568">`Text` プロパティは必須であり、200文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="9a545-568">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="9a545-569">メッセージは[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-569">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="9a545-570">アプリには、データベースコンテキストクラス (Data/AppDbContext.cs) にデータアクセス層 (DAL) が含まれています。これは、`AppDbContext` (*Data/AppDbContext.cs*) です。</span><span class="sxs-lookup"><span data-stu-id="9a545-570">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="9a545-571">アプリケーションの起動時にデータベースが空の場合、メッセージストアは3つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-571">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="9a545-572">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-572">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="9a545-573">&#8224;EF トピック「InMemory を使用した[テスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストにメモリ内データベースを使用する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="9a545-573">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="9a545-574">このトピックでは、 [xUnit](https://xunit.github.io/)テストフレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a545-574">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="9a545-575">テストの概念とテストの実装は、テストフレームワークごとに似ていますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="9a545-575">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="9a545-576">アプリはリポジトリパターンを使用せず、[作業単位 (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages はこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9a545-576">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="9a545-577">詳細については、「[インフラストラクチャの永続化層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テストコントローラーのロジック](/aspnet/core/mvc/controllers/testing)の設計 (例では、リポジトリパターンを実装します)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a545-577">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="9a545-578">テストアプリの組織</span><span class="sxs-lookup"><span data-stu-id="9a545-578">Test app organization</span></span>

<span data-ttu-id="9a545-579">テストアプリは、 *tests/RazorPagesProject.Tests*ディレクトリ内のコンソールアプリです。</span><span class="sxs-lookup"><span data-stu-id="9a545-579">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="9a545-580">テストアプリケーションディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a545-580">Test app directory</span></span> | <span data-ttu-id="9a545-581">説明</span><span class="sxs-lookup"><span data-stu-id="9a545-581">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="9a545-582">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="9a545-582">*AuthTests*</span></span> | <span data-ttu-id="9a545-583">次のテストメソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9a545-583">Contains test methods for:</span></span><ul><li><span data-ttu-id="9a545-584">認証されていないユーザーによるセキュリティで保護されたページへのアクセス。</span><span class="sxs-lookup"><span data-stu-id="9a545-584">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="9a545-585">認証されたユーザーがモック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>を使用してセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="9a545-585">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="9a545-586">GitHub ユーザープロファイルを取得し、プロファイルのユーザーログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="9a545-586">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="9a545-587">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="9a545-587">*BasicTests*</span></span> | <span data-ttu-id="9a545-588">ルーティングおよびコンテンツタイプのテストメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-588">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="9a545-589">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="9a545-589">*IntegrationTests*</span></span> | <span data-ttu-id="9a545-590">カスタム `WebApplicationFactory` クラスを使用したインデックスページの統合テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-590">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="9a545-591">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="9a545-591">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="9a545-592">*Utilities.cs*には、テストデータを使用してデータベースをシード処理するために使用される `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a545-592">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="9a545-593">*HtmlHelpers.cs*は、テストメソッドで使用するために AngleSharp `IHtmlDocument` を返すメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a545-593">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="9a545-594">*HttpClientExtensions.cs*は、`SendAsync` のオーバーロードを使用して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="9a545-594">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="9a545-595">テストフレームワークは[xUnit](https://xunit.github.io/)です。</span><span class="sxs-lookup"><span data-stu-id="9a545-595">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="9a545-596">統合テストは、 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)を含む[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a545-596">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="9a545-597">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージはテストホストとテストサーバーを構成するために使用されるため、`TestHost` および `TestServer` パッケージはテストアプリのプロジェクトファイルまたはテストの開発者構成で直接パッケージ参照を必要としません。アプリケーション.</span><span class="sxs-lookup"><span data-stu-id="9a545-597">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="9a545-598">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="9a545-598">**Seeding the database for testing**</span></span>

<span data-ttu-id="9a545-599">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-599">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="9a545-600">たとえば、削除テストでは、データベースレコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも1つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a545-600">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="9a545-601">サンプルアプリでは、テストが実行時に使用できる*Utilities.cs*内の3つのメッセージを使用してデータベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="9a545-601">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9a545-602">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9a545-602">Additional resources</span></span>

* [<span data-ttu-id="9a545-603">単体テスト</span><span class="sxs-lookup"><span data-stu-id="9a545-603">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
