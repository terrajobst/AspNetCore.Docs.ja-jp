---
title: ASP.NET Core のコントローラーのロジックをテストする
author: ardalis
description: Moq と xUnit を使って ASP.NET Core のコントローラーのロジックをテストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2019
uid: mvc/controllers/testing
ms.openlocfilehash: 8dd2fc5d581dbcb11afbcdc0c154c0e2640f9259
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622735"
---
# <a name="test-controller-logic-in-aspnet-core"></a><span data-ttu-id="7a146-103">ASP.NET Core のコントローラーのロジックをテストする</span><span class="sxs-lookup"><span data-stu-id="7a146-103">Test controller logic in ASP.NET Core</span></span>

<span data-ttu-id="7a146-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7a146-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7a146-105">[コントローラー](xref:mvc/controllers/actions)は、すべての ASP.NET Core MVC アプリで中心的な役割を担います。</span><span class="sxs-lookup"><span data-stu-id="7a146-105">[Controllers](xref:mvc/controllers/actions) play a central role in any ASP.NET Core MVC app.</span></span> <span data-ttu-id="7a146-106">そのため、コントローラーが意図するとおりに動作するという信頼が必要です。</span><span class="sxs-lookup"><span data-stu-id="7a146-106">As such, you should have confidence that controllers behave as intended.</span></span> <span data-ttu-id="7a146-107">自動テストによって、アプリが運用環境にデプロイされる前にエラーを検出できます。</span><span class="sxs-lookup"><span data-stu-id="7a146-107">Automated tests can detect errors before the app is deployed to a production environment.</span></span>

<span data-ttu-id="7a146-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7a146-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="unit-tests-of-controller-logic"></a><span data-ttu-id="7a146-109">コントローラー ロジックの単体テスト</span><span class="sxs-lookup"><span data-stu-id="7a146-109">Unit tests of controller logic</span></span>

<span data-ttu-id="7a146-110">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)では、アプリの一部をインフラストラクチャや依存関係から切り離してテストすることが必要とされます。</span><span class="sxs-lookup"><span data-stu-id="7a146-110">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="7a146-111">コントローラー ロジックの単体テストを行うと、単一のアクションの内容のみがテストされ、その依存関係やフレームワーク自体の動作はテストされません。</span><span class="sxs-lookup"><span data-stu-id="7a146-111">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

<span data-ttu-id="7a146-112">コントローラーのアクションの単体テストは、コントローラーの動作に注目するように設定します。</span><span class="sxs-lookup"><span data-stu-id="7a146-112">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="7a146-113">コントローラーの単体テストでは、[フィルター](xref:mvc/controllers/filters)、[ルーティング](xref:fundamentals/routing)、[モデル バインド](xref:mvc/models/model-binding)などのシナリオは除外します。</span><span class="sxs-lookup"><span data-stu-id="7a146-113">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7a146-114">まとまって要求に応答するコンポーネント間のインタラクションをカバーするテストは、*統合テスト*によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7a146-114">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="7a146-115">統合テストの詳細については、「<xref:test/integration-tests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a146-115">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="7a146-116">カスタム フィルターやルートを作成している場合は、コントローラーの特定のアクションに対するテストの一部としてではなく、単体テストを切り離して実行します。</span><span class="sxs-lookup"><span data-stu-id="7a146-116">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="7a146-117">コントローラーの単体テストを理解するために、次のサンプル アプリ内のコントローラーを確認してください。</span><span class="sxs-lookup"><span data-stu-id="7a146-117">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> <span data-ttu-id="7a146-118">Home コントローラーは、ブレーンストーミング セッションの一覧を表示し、POST 要求で新しいブレーンストーミング セッションを作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="7a146-118">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="7a146-119">上記のコントローラーの説明:</span><span class="sxs-lookup"><span data-stu-id="7a146-119">The preceding controller:</span></span>

* <span data-ttu-id="7a146-120">[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に従います。</span><span class="sxs-lookup"><span data-stu-id="7a146-120">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="7a146-121">[依存関係の注入 (DI)](xref:fundamentals/dependency-injection) を予期して、`IBrainstormSessionRepository` のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7a146-121">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="7a146-122">モック オブジェクト フレームワークを使用するモックされた `IBrainstormSessionRepository` サービスでテストできます ([Moq](https://www.nuget.org/packages/Moq/) サービスなど)。</span><span class="sxs-lookup"><span data-stu-id="7a146-122">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="7a146-123">*モック オブジェクト*は、テストで使用されるプロパティとメソッドの動作が事前定義されている加工オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="7a146-123">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="7a146-124">詳細については、「[Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests)」(統合テストの概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a146-124">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="7a146-125">`HTTP GET Index` メソッドにはループや分岐はなく、1 つのメソッドを呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="7a146-125">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="7a146-126">このアクションに対する単体テストでは、以下を行います。</span><span class="sxs-lookup"><span data-stu-id="7a146-126">The unit test for this action:</span></span>

* <span data-ttu-id="7a146-127">`IBrainstormSessionRepository` メソッドを使用する `GetTestSessions` サービスをモックする。</span><span class="sxs-lookup"><span data-stu-id="7a146-127">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="7a146-128">`GetTestSessions` が日付とセッション名を持つ 2 つのモック ブレーンストーミング セッションを作成する。</span><span class="sxs-lookup"><span data-stu-id="7a146-128">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="7a146-129">`Index` メソッドを実行する。</span><span class="sxs-lookup"><span data-stu-id="7a146-129">Executes the `Index` method.</span></span>
* <span data-ttu-id="7a146-130">メソッドによって返された結果に関するアサーションを行う。</span><span class="sxs-lookup"><span data-stu-id="7a146-130">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="7a146-131"><xref:Microsoft.AspNetCore.Mvc.ViewResult> が返される。</span><span class="sxs-lookup"><span data-stu-id="7a146-131">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="7a146-132">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) は `StormSessionViewModel`。</span><span class="sxs-lookup"><span data-stu-id="7a146-132">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="7a146-133">`ViewDataDictionary.Model`に格納された 2 つのブレーンストーミング セッションがある。</span><span class="sxs-lookup"><span data-stu-id="7a146-133">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="7a146-134">Home コントローラーの `HTTP POST Index` メソッドのテストでは、以下が検証されます。</span><span class="sxs-lookup"><span data-stu-id="7a146-134">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="7a146-135">[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) が `false` の場合、アクション メソッドは、*400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> と適切なデータを返す。</span><span class="sxs-lookup"><span data-stu-id="7a146-135">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="7a146-136">`ModelState.IsValid` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="7a146-136">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="7a146-137">リポジトリの `Add` メソッドが呼び出される。</span><span class="sxs-lookup"><span data-stu-id="7a146-137">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="7a146-138"><xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> と適切な引数が返される。</span><span class="sxs-lookup"><span data-stu-id="7a146-138">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="7a146-139">下の最初のテストに示すように、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> を使用してエラーを追加することで、モデルの無効状態をテストできます。</span><span class="sxs-lookup"><span data-stu-id="7a146-139">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="7a146-140">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が有効でない場合は、GET 要求と同じ `ViewResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="7a146-140">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="7a146-141">このテストでは、無効なモデルを渡すことは試していません。</span><span class="sxs-lookup"><span data-stu-id="7a146-141">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="7a146-142">モデル バインドが実行されていないため、無効なモデルを渡すことは効果的なアプローチではありません (ただし、[統合テスト](xref:test/integration-tests)ではモデル バインドが使用されます)。</span><span class="sxs-lookup"><span data-stu-id="7a146-142">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="7a146-143">ここでは、モデル バインドはテストされていません。</span><span class="sxs-lookup"><span data-stu-id="7a146-143">In this case, model binding isn't tested.</span></span> <span data-ttu-id="7a146-144">これらの単体テストでは、アクション メソッドのコードだけをテストしています。</span><span class="sxs-lookup"><span data-stu-id="7a146-144">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="7a146-145">2 番目のテストでは、`ModelState` が有効な場合の検証を行います。</span><span class="sxs-lookup"><span data-stu-id="7a146-145">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="7a146-146">新しい `BrainstormSession` が追加される (リポジトリ経由)。</span><span class="sxs-lookup"><span data-stu-id="7a146-146">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="7a146-147">メソッドが `RedirectToActionResult` と予期されるプロパティを返す。</span><span class="sxs-lookup"><span data-stu-id="7a146-147">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="7a146-148">呼び出されないモックの呼び出しは通常は無視されますが、Setup 呼び出しの最後で `Verifiable` を呼び出すと、テスト内でのモック検証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="7a146-148">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="7a146-149">これは `mockRepo.Verify` の呼び出しで実行され、予期されるメソッドが呼び出されないとテストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="7a146-149">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="7a146-150">このサンプルで使われている Moq ライブラリにより、検証可能な ("厳密な") モックと検証不可能なモック ("厳密でない" モックまたはスタブとも呼ばれます) を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="7a146-150">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="7a146-151">詳しくは、Moq の「[Customizing Mock Behavior](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)」(モックの動作のカスタマイズ) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7a146-151">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="7a146-152">サンプル アプリの [SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) は、特定のブレーンストーミング セッションに関する情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="7a146-152">[SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="7a146-153">このコントローラーには、無効な `id` 値を処理するロジックが含まれています (これらのシナリオをカバーするために、次のサンプルには 2 つの `return` シナリオがあります)。</span><span class="sxs-lookup"><span data-stu-id="7a146-153">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="7a146-154">最終的な `return` ステートメントにより、新しい `StormSessionViewModel` がビュー (*Controllers/SessionController.cs*) に返されます。</span><span class="sxs-lookup"><span data-stu-id="7a146-154">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="7a146-155">Session コントローラーの `Index`アクション内に各 `return` シナリオ用の 1 つのテストを含む単体テスト:</span><span class="sxs-lookup"><span data-stu-id="7a146-155">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="7a146-156">Ideas コントローラーに移ると、アプリは、機能を Web API として `api/ideas` ルートで公開します。</span><span class="sxs-lookup"><span data-stu-id="7a146-156">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="7a146-157">ブレーンストーミング セッションに関連付けられたアイデアの一覧 (`IdeaDTO`) が、`ForSession` メソッドによって返される。</span><span class="sxs-lookup"><span data-stu-id="7a146-157">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="7a146-158">`Create` メソッドが新しいアイデアをセッションに追加する。</span><span class="sxs-lookup"><span data-stu-id="7a146-158">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="7a146-159">API の呼び出しでビジネス ドメイン エンティティを直接返すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="7a146-159">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="7a146-160">ドメイン エンティティ:</span><span class="sxs-lookup"><span data-stu-id="7a146-160">Domain entities:</span></span>

* <span data-ttu-id="7a146-161">多くの場合、クライアントが必要とするもの以上のデータを含みます。</span><span class="sxs-lookup"><span data-stu-id="7a146-161">Often include more data than the client requires.</span></span>
* <span data-ttu-id="7a146-162">アプリの内部ドメイン モデルと一般公開されている API が不必要に結合します。</span><span class="sxs-lookup"><span data-stu-id="7a146-162">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="7a146-163">ドメイン エンティティとクライアントに返される型の間のマッピングは、以下のように実行できます。</span><span class="sxs-lookup"><span data-stu-id="7a146-163">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="7a146-164">サンプル アプリで使用しているように、LINQ `Select` を使用して手動で実行します。</span><span class="sxs-lookup"><span data-stu-id="7a146-164">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="7a146-165">詳細については、「[LINQ (統合言語クエリ)](/dotnet/standard/using-linq)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a146-165">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="7a146-166">[AutoMapper](https://github.com/AutoMapper/AutoMapper) などのライブラリを使用して自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="7a146-166">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="7a146-167">次のサンプル アプリは、Ideas コントローラーの `Create` と `ForSession` API メソッドの単体テストを示します。</span><span class="sxs-lookup"><span data-stu-id="7a146-167">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="7a146-168">このサンプル アプリには、2 つの `ForSession` テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a146-168">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="7a146-169">最初のテストでは、無効なセッションに対して `ForSession` が <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) を返すかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="7a146-169">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="7a146-170">2 番目の `ForSession` テストでは、有効なセッションに対して `ForSession` がセッションのアイデアの一覧 (`<List<IdeaDTO>>`) を返すかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="7a146-170">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="7a146-171">これらのチェックでは、最初のアイデアを調べて、その `Name` プロパティが正しいことも確認します。</span><span class="sxs-lookup"><span data-stu-id="7a146-171">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="7a146-172">`ModelState` が無効の場合の `Create` メソッドの動作をテストするため、サンプル アプリでは、テストの一部としてコントローラーにモデル エラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="7a146-172">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="7a146-173">単体テストでは、モデルの検証またはモデル バインドのテストを試さないでください。無効な `ModelState` に遭遇したときのアクション メソッドの動作だけをテストしてください。</span><span class="sxs-lookup"><span data-stu-id="7a146-173">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="7a146-174">`Create` の 2 番目のテストでは、リポジトリが `null` を返すことに依存しているため、`null` を返すようにモック リポジトリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="7a146-174">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="7a146-175">テスト データベース (メモリ内またはそれ以外の場所) を作成し、この結果を返すクエリを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7a146-175">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="7a146-176">サンプル コードに示すように、このテストは単一のステートメントで実行できます。</span><span class="sxs-lookup"><span data-stu-id="7a146-176">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="7a146-177">3 番目の `Create` テスト (`Create_ReturnsNewlyCreatedIdeaForSession`) では、リポジトリの `UpdateAsync` メソッドが呼び出されることを検証します。</span><span class="sxs-lookup"><span data-stu-id="7a146-177">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="7a146-178">モックが `Verifiable` で呼び出され、モック リポジトリの `Verify` メソッドが呼び出されて、検証可能なメソッドが実行されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7a146-178">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="7a146-179">`UpdateAsync` メソッドがデータを保存したことを確認するのは、単体テストの役割ではありません。それは統合テストで実行できます。</span><span class="sxs-lookup"><span data-stu-id="7a146-179">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultlttgt"></a><span data-ttu-id="7a146-180">ActionResult&lt;T&gt; をテストする</span><span class="sxs-lookup"><span data-stu-id="7a146-180">Test ActionResult&lt;T&gt;</span></span>

<span data-ttu-id="7a146-181">ASP.NET Core 2.1 以降、[ActionResult&lt;T&gt;](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) で、`ActionResult` から派生する型または特定の型を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="7a146-181">In ASP.NET Core 2.1 or later, [ActionResult&lt;T&gt;](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="7a146-182">サンプル アプリには、特定のセッション `id` に対して `List<IdeaDTO>` を返すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a146-182">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="7a146-183">セッションに `id` が存在しない場合、コントローラーは <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> を返します。</span><span class="sxs-lookup"><span data-stu-id="7a146-183">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="7a146-184">`ApiIdeasControllerTests`には、`ForSessionActionResult` コントローラーの 2 つのテストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a146-184">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="7a146-185">最初のテストでは、コントローラーは `ActionResult` を返すが、存在しないセッション `id` の存在しないアイデアの一覧は返さないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="7a146-185">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="7a146-186">`ActionResult` の型は `ActionResult<List<IdeaDTO>>`。</span><span class="sxs-lookup"><span data-stu-id="7a146-186">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="7a146-187"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> は <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="7a146-187">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="7a146-188">有効なセッション `id` に対する 2 番目のテストでは、メソッドが以下を返すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="7a146-188">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="7a146-189">`List<IdeaDTO>` 型の `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="7a146-189">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="7a146-190">[ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) は `List<IdeaDTO>` 型。</span><span class="sxs-lookup"><span data-stu-id="7a146-190">The [ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="7a146-191">一覧の最初の項目は、モック セッションに格納されているアイデアと一致する有効なアイデア (`GetTestSession` の呼び出しによって取得)。</span><span class="sxs-lookup"><span data-stu-id="7a146-191">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="7a146-192">このサンプル アプリには、特定のセッションで新しい `Idea` を作成するメソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a146-192">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="7a146-193">コントローラーは以下を返します。</span><span class="sxs-lookup"><span data-stu-id="7a146-193">The controller returns:</span></span>

* <span data-ttu-id="7a146-194">無効なモデルに対する <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>。</span><span class="sxs-lookup"><span data-stu-id="7a146-194"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="7a146-195">セッションが存在しない場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>。</span><span class="sxs-lookup"><span data-stu-id="7a146-195"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="7a146-196">セッションが新しいアイデアで更新された場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>。</span><span class="sxs-lookup"><span data-stu-id="7a146-196"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="7a146-197">`ApiIdeasControllerTests` には、`CreateActionResult` の 3 つのテストが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7a146-197">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="7a146-198">最初のテストでは、無効なモデルに対して <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> が返されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7a146-198">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="7a146-199">2 番目のテストでは、セッションが存在しない場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> が返されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7a146-199">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="7a146-200">有効なセッション `id` に対しては、最後のテストで以下を確認します。</span><span class="sxs-lookup"><span data-stu-id="7a146-200">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="7a146-201">メソッドが `BrainstormSession` 型の `ActionResult` を返す。</span><span class="sxs-lookup"><span data-stu-id="7a146-201">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="7a146-202">[ActionResult&lt;T&gt;.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) は <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>。</span><span class="sxs-lookup"><span data-stu-id="7a146-202">The [ActionResult&lt;T&gt;.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="7a146-203">`CreatedAtActionResult` は *201 Created* 応答に類似した `Location` ヘッダー付きの応答である。</span><span class="sxs-lookup"><span data-stu-id="7a146-203">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="7a146-204">[ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) は `BrainstormSession` 型。</span><span class="sxs-lookup"><span data-stu-id="7a146-204">The [ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="7a146-205">セッション `UpdateAsync(testSession)` を更新するモック 呼び出しが呼び出された。</span><span class="sxs-lookup"><span data-stu-id="7a146-205">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="7a146-206">`Verifiable` メソッド呼び出しは、アサーション内で `mockRepo.Verify()` を実行することでチェックされます。</span><span class="sxs-lookup"><span data-stu-id="7a146-206">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="7a146-207">セッションに対して 2 つの `Idea` オブジェクトが返された。</span><span class="sxs-lookup"><span data-stu-id="7a146-207">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="7a146-208">最後の項目 (`UpdateAsync` へのモック呼び出しによって追加された `Idea`) が、テスト中にセッションに追加された `newIdea` と一致する。</span><span class="sxs-lookup"><span data-stu-id="7a146-208">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

## <a name="additional-resources"></a><span data-ttu-id="7a146-209">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7a146-209">Additional resources</span></span>

* <xref:test/integration-tests>
* <span data-ttu-id="7a146-210">[Visual Studio で単体テストを作成して実行します](/visualstudio/test/unit-test-your-code)。</span><span class="sxs-lookup"><span data-stu-id="7a146-210">[Create and run unit tests with Visual Studio](/visualstudio/test/unit-test-your-code).</span></span>
