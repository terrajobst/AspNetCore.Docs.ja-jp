---
title: ASP.NET Core Blazor アプリでのエラーの処理
author: guardrex
description: Blazor が未処理の例外をどのように管理するか、およびエラーを検出して処理するアプリを開発する方法について、ASP.NET Core Blazor 方法を説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: blazor/handle-errors
ms.openlocfilehash: afcaa4d926c3e5f0a018897ce4b67b54574dae77
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426990"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="13ec7-103">ASP.NET Core Blazor アプリでのエラーの処理</span><span class="sxs-lookup"><span data-stu-id="13ec7-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="13ec7-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="13ec7-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="13ec7-105">この記事では、Blazor が未処理の例外を管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a><span data-ttu-id="13ec7-106">Blazor フレームワークがハンドルされない例外にどのように反応するか</span><span class="sxs-lookup"><span data-stu-id="13ec7-106">How the Blazor framework reacts to unhandled exceptions</span></span>

<span data-ttu-id="13ec7-107">Blazor Server は、ステートフルフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="13ec7-107">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="13ec7-108">ユーザーは、アプリを操作している間、*回線*と呼ばれるサーバーへの接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-108">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="13ec7-109">回線は、アクティブなコンポーネントインスタンスに加えて、次のような状態の他の多くの側面を保持します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-109">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="13ec7-110">コンポーネントの最新のレンダリング出力。</span><span class="sxs-lookup"><span data-stu-id="13ec7-110">The most recent rendered output of components.</span></span>
* <span data-ttu-id="13ec7-111">クライアント側のイベントによってトリガーされる可能性がある、現在のイベント処理デリゲートのセット。</span><span class="sxs-lookup"><span data-stu-id="13ec7-111">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="13ec7-112">ユーザーが複数のブラウザータブでアプリを開いた場合、複数の独立した回路があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-112">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="13ec7-113">Blazor は、ほとんどのハンドルされない例外を、発生した回線にとって致命的なものとして扱います。</span><span class="sxs-lookup"><span data-stu-id="13ec7-113">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="13ec7-114">ハンドルされない例外によって回線が終了した場合、ユーザーは新しい回線を作成するためにページを再読み込みするだけで、アプリとの対話を続けることができます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-114">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="13ec7-115">他のユーザーまたは他のブラウザータブの回線である、終了した回路以外の回路は影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-115">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="13ec7-116">このシナリオは、クラッシュしたアプリを再起動する必要がある&mdash;クラッシュするデスクトップアプリに似ていますが、他のアプリは影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-116">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="13ec7-117">次の理由により、ハンドルされない例外が発生したときに回線が終了します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-117">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="13ec7-118">ハンドルされない例外が発生すると、回線が未定義の状態のままになることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-118">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="13ec7-119">未処理の例外が発生した後は、アプリの通常の動作を保証できません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-119">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="13ec7-120">回線が継続している場合は、セキュリティの脆弱性がアプリに表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-120">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="13ec7-121">開発者コードでハンドルされない例外を管理する</span><span class="sxs-lookup"><span data-stu-id="13ec7-121">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="13ec7-122">エラー発生後もアプリを続行するには、アプリにエラー処理ロジックが必要です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-122">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="13ec7-123">この記事の後のセクションでは、未処理の例外の潜在的な原因について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-123">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="13ec7-124">実稼働環境では、フレームワークの例外メッセージやスタックトレースを UI に表示しません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-124">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="13ec7-125">例外メッセージまたはスタックトレースを表示すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-125">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="13ec7-126">エンドユーザーに機密情報を開示します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-126">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="13ec7-127">悪意のあるユーザーがアプリ、サーバー、またはネットワークのセキュリティを侵害する可能性のある脆弱性を検出するのを支援します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-127">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="13ec7-128">永続的なプロバイダーでエラーをログに記録する</span><span class="sxs-lookup"><span data-stu-id="13ec7-128">Log errors with a persistent provider</span></span>

<span data-ttu-id="13ec7-129">未処理の例外が発生した場合、例外は、サービスコンテナーに構成されている <xref:Microsoft.Extensions.Logging.ILogger> インスタンスに記録されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-129">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="13ec7-130">既定では、Blazor apps はコンソールログプロバイダーを使用してコンソール出力にログを記録します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-130">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="13ec7-131">ログサイズとログローテーションを管理するプロバイダーを使用して、より永続的な場所にログを記録することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-131">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="13ec7-132">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-132">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="13ec7-133">開発中、Blazor は通常、デバッグを支援するために、例外の完全な詳細をブラウザーのコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-133">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="13ec7-134">運用環境では、ブラウザーのコンソールでの詳細なエラーは既定で無効になっています。つまり、エラーはクライアントに送信されませんが、例外の完全な詳細は引き続きサーバー側でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-134">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="13ec7-135">詳細については、「<xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-135">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="13ec7-136">ログに記録するインシデントと、ログに記録されるインシデントの重大度レベルを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-136">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="13ec7-137">悪意のあるユーザーは、意図的にエラーをトリガーできる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-137">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="13ec7-138">たとえば、製品の詳細を表示するコンポーネントの URL に不明な `ProductId` が指定されている場合は、エラーからインシデントをログに記録しないようにします。</span><span class="sxs-lookup"><span data-stu-id="13ec7-138">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="13ec7-139">すべてのエラーを、ログ記録の重要度の高いインシデントとして処理することはできません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-139">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="13ec7-140">エラーが発生する可能性のある場所</span><span class="sxs-lookup"><span data-stu-id="13ec7-140">Places where errors may occur</span></span>

<span data-ttu-id="13ec7-141">フレームワークとアプリコードは、次のいずれかの場所でハンドルされない例外をトリガーすることがあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-141">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="13ec7-142">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="13ec7-142">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="13ec7-143">ライフサイクルメソッド</span><span class="sxs-lookup"><span data-stu-id="13ec7-143">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="13ec7-144">レンダリングロジック</span><span class="sxs-lookup"><span data-stu-id="13ec7-144">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="13ec7-145">イベントハンドラー</span><span class="sxs-lookup"><span data-stu-id="13ec7-145">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="13ec7-146">コンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="13ec7-146">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="13ec7-147">JavaScript の相互運用</span><span class="sxs-lookup"><span data-stu-id="13ec7-147">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="13ec7-148">サーキットハンドラー</span><span class="sxs-lookup"><span data-stu-id="13ec7-148">Circuit handlers</span></span>](#circuit-handlers)
* [<span data-ttu-id="13ec7-149">回線破棄</span><span class="sxs-lookup"><span data-stu-id="13ec7-149">Circuit disposal</span></span>](#circuit-disposal)
* [<span data-ttu-id="13ec7-150">プリ</span><span class="sxs-lookup"><span data-stu-id="13ec7-150">Prerendering</span></span>](#prerendering)

<span data-ttu-id="13ec7-151">上記のハンドルされない例外については、この記事の次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-151">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="13ec7-152">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="13ec7-152">Component instantiation</span></span>

<span data-ttu-id="13ec7-153">Blazor がコンポーネントのインスタンスを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="13ec7-153">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="13ec7-154">コンポーネントのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-154">The component's constructor is invoked.</span></span>
* <span data-ttu-id="13ec7-155">[@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)ディレクティブまたは[[挿入]](xref:blazor/dependency-injection#request-a-service-in-a-component)属性を使用して、コンポーネントのコンストラクターに渡される非シングルトン DI サービスのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-155">The constructors of any non-singleton DI services supplied to the component's constructor via the [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [[Inject]](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span> 

<span data-ttu-id="13ec7-156">任意の `[Inject]` プロパティに対して実行されるコンストラクターまたは setter がハンドルされない例外をスローすると、回線が失敗します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-156">A circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="13ec7-157">フレームワークはコンポーネントをインスタンス化できないため、例外は fatal です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-157">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="13ec7-158">コンストラクターのロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む[try-catch ステートメントを](/dotnet/csharp/language-reference/keywords/try-catch)使用して例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-158">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="13ec7-159">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="13ec7-159">Lifecycle methods</span></span>

<span data-ttu-id="13ec7-160">コンポーネントの有効期間中、Blazor はライフサイクルメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-160">During the lifetime of a component, Blazor invokes lifecycle methods:</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="13ec7-161">あるライフサイクルメソッドが例外を同期的または非同期的にスローする場合、この例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-161">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to the circuit.</span></span> <span data-ttu-id="13ec7-162">コンポーネントがライフサイクルメソッドのエラーに対処するには、エラー処理ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-162">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="13ec7-163">次の例では、`OnParametersSetAsync` がメソッドを呼び出して製品を取得します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-163">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="13ec7-164">`ProductRepository.GetProductByIdAsync` メソッドでスローされた例外は、`try-catch` ステートメントによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-164">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="13ec7-165">`catch` ブロックを実行すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-165">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="13ec7-166">`loadFailed` は `true`に設定されます。これは、ユーザーにエラーメッセージを表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-166">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="13ec7-167">エラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-167">The error is logged.</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="13ec7-168">レンダリングロジック</span><span class="sxs-lookup"><span data-stu-id="13ec7-168">Rendering logic</span></span>

<span data-ttu-id="13ec7-169">`.razor` コンポーネントファイル内の宣言マークアップは、`BuildRenderTree`とC#呼ばれるメソッドにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-169">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="13ec7-170">コンポーネントがレンダリングされると、`BuildRenderTree` が実行され、レンダリングされたコンポーネントの要素、テキスト、および子コンポーネントを記述するデータ構造が構築されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-170">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="13ec7-171">レンダリングロジックは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-171">Rendering logic can throw an exception.</span></span> <span data-ttu-id="13ec7-172">このシナリオの例は、`@someObject.PropertyName` が評価され、`@someObject` が `null`場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-172">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="13ec7-173">レンダリングロジックによってスローされた未処理の例外は、回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-173">An unhandled exception thrown by rendering logic is fatal to the circuit.</span></span>

<span data-ttu-id="13ec7-174">レンダリングロジックで null 参照例外が発生しないようにするには、そのメンバーにアクセスする前に `null` オブジェクトを確認します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-174">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="13ec7-175">次の例では、`person.Address` が `null`場合、`person.Address` のプロパティにはアクセスしません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-175">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="13ec7-176">上記のコードでは、`person` が `null`でないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="13ec7-176">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="13ec7-177">多くの場合、コードの構造によって、コンポーネントのレンダリング時にオブジェクトが存在することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-177">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="13ec7-178">そのような場合は、表示ロジックで `null` を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-178">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="13ec7-179">前の例では、コンポーネントがインスタンス化されるときに `person` が作成されるため、`person` が確実に存在することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-179">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="13ec7-180">イベント ハンドラー</span><span class="sxs-lookup"><span data-stu-id="13ec7-180">Event handlers</span></span>

<span data-ttu-id="13ec7-181">クライアント側のコードは、次C#を使用してイベントハンドラーが作成されるときに、コードの呼び出しをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="13ec7-181">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="13ec7-182">その他の `@on...` 属性</span><span class="sxs-lookup"><span data-stu-id="13ec7-182">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="13ec7-183">これらのシナリオでは、イベントハンドラーコードによってハンドルされない例外がスローされることがあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-183">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="13ec7-184">イベントハンドラーがハンドルされない例外をスローした場合 (たとえば、データベースクエリが失敗した場合)、その例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-184">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to the circuit.</span></span> <span data-ttu-id="13ec7-185">アプリが外部の理由で失敗する可能性のあるコードを呼び出した場合は、エラー処理とログ記録を含む[try-catch ステートメントを使用して例外](/dotnet/csharp/language-reference/keywords/try-catch)をトラップします。</span><span class="sxs-lookup"><span data-stu-id="13ec7-185">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="13ec7-186">ユーザーコードによって例外がトラップされて処理されない場合は、フレームワークによって例外がログに記録され、回線が終了します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-186">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="13ec7-187">コンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="13ec7-187">Component disposal</span></span>

<span data-ttu-id="13ec7-188">たとえば、ユーザーが別のページに移動したため、コンポーネントが UI から削除されることがあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-188">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="13ec7-189"><xref:System.IDisposable?displayProperty=fullName> を実装するコンポーネントが UI から削除されると、フレームワークはコンポーネントの <xref:System.IDisposable.Dispose*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-189">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span> 

<span data-ttu-id="13ec7-190">コンポーネントの `Dispose` メソッドがハンドルされない例外をスローした場合、この例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-190">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="13ec7-191">破棄ロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む[try-catch ステートメントを](/dotnet/csharp/language-reference/keywords/try-catch)使用して例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-191">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="13ec7-192">コンポーネントの破棄の詳細については、「<xref:blazor/components#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-192">For more information on component disposal, see <xref:blazor/components#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="13ec7-193">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="13ec7-193">JavaScript interop</span></span>

<span data-ttu-id="13ec7-194">`IJSRuntime.InvokeAsync<T>` を使用すると、.NET コードは、ユーザーのブラウザーで JavaScript ランタイムへの非同期呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-194">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="13ec7-195">`InvokeAsync<T>`でのエラー処理には、次の条件が適用されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-195">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="13ec7-196">`InvokeAsync<T>` の呼び出しが同期的に失敗した場合、.NET 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-196">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="13ec7-197">たとえば、指定された引数をシリアル化できないため、`InvokeAsync<T>` を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-197">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="13ec7-198">開発者コードは例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-198">Developer code must catch the exception.</span></span> <span data-ttu-id="13ec7-199">イベントハンドラーまたはコンポーネントのライフサイクルメソッドのアプリコードで例外が処理されない場合、結果として得られる例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-199">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to the circuit.</span></span>
* <span data-ttu-id="13ec7-200">`InvokeAsync<T>` の呼び出しが非同期に失敗した場合、.NET <xref:System.Threading.Tasks.Task> は失敗します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-200">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="13ec7-201">たとえば、JavaScript 側のコードが例外をスローしたり、`rejected`として完了した `Promise` を返したりするために、`InvokeAsync<T>` の呼び出しが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-201">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="13ec7-202">開発者コードは例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-202">Developer code must catch the exception.</span></span> <span data-ttu-id="13ec7-203">[Await](/dotnet/csharp/language-reference/keywords/await)演算子を使用する場合は、エラー処理とログ記録を使用し[て、try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでメソッド呼び出しをラップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-203">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="13ec7-204">それ以外の場合、失敗したコードは、回路にとって致命的な未処理の例外を発生させることになります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-204">Otherwise, the failing code results in an unhandled exception that's fatal to the circuit.</span></span>
* <span data-ttu-id="13ec7-205">既定では、`InvokeAsync<T>` の呼び出しは特定の期間内に完了する必要があります。そうでない場合は、呼び出しがタイムアウトします。既定のタイムアウト期間は1分です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-205">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="13ec7-206">タイムアウトは、完了メッセージを返信しないネットワーク接続または JavaScript コードの損失からコードを保護します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-206">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="13ec7-207">呼び出しがタイムアウトした場合、結果として得られる `Task` は <xref:System.OperationCanceledException>で失敗します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-207">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="13ec7-208">ログ記録で例外をトラップして処理します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-208">Trap and process the exception with logging.</span></span>

<span data-ttu-id="13ec7-209">同様に、JavaScript コードでは、 [[JSInvokable] 属性](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)によって示される .net メソッドの呼び出しを開始できます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-209">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [[JSInvokable] attribute](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions).</span></span> <span data-ttu-id="13ec7-210">これらの .NET メソッドでハンドルされない例外がスローされた場合:</span><span class="sxs-lookup"><span data-stu-id="13ec7-210">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="13ec7-211">この例外は、回線にとって致命的な例外として扱われません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-211">The exception isn't treated as fatal to the circuit.</span></span>
* <span data-ttu-id="13ec7-212">JavaScript 側の `Promise` は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-212">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="13ec7-213">.NET 側またはメソッド呼び出しの JavaScript 側でエラー処理コードを使用するオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-213">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="13ec7-214">詳細については、「<xref:blazor/javascript-interop>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-214">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="circuit-handlers"></a><span data-ttu-id="13ec7-215">サーキットハンドラー</span><span class="sxs-lookup"><span data-stu-id="13ec7-215">Circuit handlers</span></span>

<span data-ttu-id="13ec7-216">Blazor を使用すると、コードで*サーキットハンドラー*を定義し、ユーザーの回線の状態が変化したときに通知を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-216">Blazor allows code to define a *circuit handler*, which receives notifications when the state of a user's circuit changes.</span></span> <span data-ttu-id="13ec7-217">次の状態が使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-217">The following states are used:</span></span>

* `initialized`
* `connected`
* `disconnected`
* `disposed`

<span data-ttu-id="13ec7-218">通知は、`CircuitHandler` 抽象基本クラスから継承する DI サービスを登録することによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-218">Notifications are managed by registering a DI service that inherits from the `CircuitHandler` abstract base class.</span></span>

<span data-ttu-id="13ec7-219">カスタムサーキットハンドラーのメソッドがハンドルされない例外をスローした場合、この例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-219">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="13ec7-220">ハンドラーのコードまたはメソッドで例外が許容されるようにするには、エラー処理とログ記録を含む1つまたは複数の[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでコードをラップします。</span><span class="sxs-lookup"><span data-stu-id="13ec7-220">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="circuit-disposal"></a><span data-ttu-id="13ec7-221">回線破棄</span><span class="sxs-lookup"><span data-stu-id="13ec7-221">Circuit disposal</span></span>

<span data-ttu-id="13ec7-222">ユーザーが切断され、フレームワークが回線の状態をクリーンアップしているために回線が終了すると、フレームワークは回線の DI スコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-222">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="13ec7-223">スコープを破棄すると、<xref:System.IDisposable?displayProperty=fullName>を実装するサーキットスコープの DI サービスが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-223">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="13ec7-224">破棄中にいずれかの DI サービスがハンドルされない例外をスローした場合、フレームワークは例外をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-224">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="prerendering"></a><span data-ttu-id="13ec7-225">プリ</span><span class="sxs-lookup"><span data-stu-id="13ec7-225">Prerendering</span></span>

<span data-ttu-id="13ec7-226">Blazor コンポーネントは、レンダリングされた HTML マークアップがユーザーの初期 HTTP 要求の一部として返されるように `Html.RenderComponentAsync` を使用して prerendered できます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-226">Blazor components can be prerendered using `Html.RenderComponentAsync` so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="13ec7-227">これは次のように機能します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-227">This works by:</span></span>

* <span data-ttu-id="13ec7-228">同じページに含まれるすべての prerendered コンポーネントに対して新しい回線を作成します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-228">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="13ec7-229">初期 HTML を生成しています。</span><span class="sxs-lookup"><span data-stu-id="13ec7-229">Generating the initial HTML.</span></span>
* <span data-ttu-id="13ec7-230">ユーザーのブラウザーが同じサーバーに SignalR 接続を確立するまで、回線を `disconnected` として扱います。</span><span class="sxs-lookup"><span data-stu-id="13ec7-230">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="13ec7-231">接続が確立されると、回線の対話機能が再開され、コンポーネントの HTML マークアップが更新されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-231">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="13ec7-232">たとえば、ライフサイクルメソッドやレンダリングロジックで、コンポーネントによって処理されない例外がスローされた場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-232">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="13ec7-233">この例外は、回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-233">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="13ec7-234">例外は、`Html.RenderComponentAsync` 呼び出しからの呼び出し履歴によってスローされます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-234">The exception is thrown up the call stack from the `Html.RenderComponentAsync` call.</span></span> <span data-ttu-id="13ec7-235">したがって、例外が開発者コードによって明示的にキャッチされない限り、HTTP 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-235">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="13ec7-236">通常の状況では、事前設定に失敗した場合に、コンポーネントのビルドとレンダリングを続行しても意味がありません。これは、作業コンポーネントをレンダリングできないためです。</span><span class="sxs-lookup"><span data-stu-id="13ec7-236">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="13ec7-237">プリレンダリング中に発生する可能性のあるエラーを許容するには、例外をスローする可能性のあるコンポーネント内にエラー処理ロジックを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-237">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="13ec7-238">エラー処理とログ記録では、 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-238">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="13ec7-239">`try-catch` ステートメントで `RenderComponentAsync` の呼び出しをラップするのではなく、`RenderComponentAsync`によって表示されるコンポーネントにエラー処理ロジックを配置します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-239">Instead of wrapping the call to `RenderComponentAsync` in a `try-catch` statement, place error handling logic in the component rendered by `RenderComponentAsync`.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="13ec7-240">高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="13ec7-240">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="13ec7-241">再帰的なレンダリング</span><span class="sxs-lookup"><span data-stu-id="13ec7-241">Recursive rendering</span></span>

<span data-ttu-id="13ec7-242">コンポーネントは再帰的に入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-242">Components can be nested recursively.</span></span> <span data-ttu-id="13ec7-243">これは、再帰的なデータ構造を表す場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="13ec7-243">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="13ec7-244">たとえば、`TreeNode` コンポーネントでは、ノードの子ごとにより多くの `TreeNode` コンポーネントをレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-244">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="13ec7-245">再帰的にレンダリングする場合は、無限再帰になるようなコーディングパターンを避けます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-245">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="13ec7-246">循環を含むデータ構造を再帰的に表示することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-246">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="13ec7-247">たとえば、子を含むツリーノードは表示しません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-247">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="13ec7-248">循環を含むレイアウトのチェーンを作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-248">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="13ec7-249">たとえば、レイアウトがそれ自体であるレイアウトを作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="13ec7-249">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="13ec7-250">悪意のあるデータ入力または JavaScript の相互運用呼び出しによって、エンドユーザーが再帰不変 (ルール) に違反しないようにします。</span><span class="sxs-lookup"><span data-stu-id="13ec7-250">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="13ec7-251">レンダリング中の無限ループ:</span><span class="sxs-lookup"><span data-stu-id="13ec7-251">Infinite loops during rendering:</span></span>

* <span data-ttu-id="13ec7-252">レンダリングプロセスを永久に続行します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-252">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="13ec7-253">は、終了しないループを作成するのと同じです。</span><span class="sxs-lookup"><span data-stu-id="13ec7-253">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="13ec7-254">これらのシナリオでは、影響を受ける回線がハングし、スレッドは通常次を試行します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-254">In these scenarios, the affected circuit hangs, and the thread usually attempts to:</span></span>

* <span data-ttu-id="13ec7-255">オペレーティングシステムで許可されている CPU 時間を無期限に使用します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-255">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="13ec7-256">無制限の数のサーバーメモリを消費します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-256">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="13ec7-257">メモリを無制限に使用することは、反復処理のたびに終了しないループによってコレクションにエントリが追加されるシナリオと同じです。</span><span class="sxs-lookup"><span data-stu-id="13ec7-257">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="13ec7-258">無限の再帰パターンを回避するには、再帰的なレンダリングコードに適切な停止条件が含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="13ec7-258">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="13ec7-259">カスタムレンダリングツリーのロジック</span><span class="sxs-lookup"><span data-stu-id="13ec7-259">Custom render tree logic</span></span>

<span data-ttu-id="13ec7-260">ほとんどの Blazor コンポーネントは、razor ファイルとして実装され、出力を表示するために `RenderTreeBuilder` で動作するロジックを生成するためにコンパイルされ*ます。*</span><span class="sxs-lookup"><span data-stu-id="13ec7-260">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="13ec7-261">開発者は、手続きC#型コードを使用して `RenderTreeBuilder` ロジックを手動で実装できます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-261">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="13ec7-262">詳細については、「<xref:blazor/components#manual-rendertreebuilder-logic>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-262">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="13ec7-263">手動のレンダーツリービルダーロジックの使用は、高度で安全ではないシナリオと見なされます。一般的なコンポーネントの開発にはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="13ec7-263">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="13ec7-264">`RenderTreeBuilder` コードが記述されている場合、開発者はコードの正確性を保証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-264">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="13ec7-265">たとえば、開発者は次のことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-265">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="13ec7-266">`OpenElement` と `CloseElement` の呼び出しが正しく調整されています。</span><span class="sxs-lookup"><span data-stu-id="13ec7-266">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="13ec7-267">属性は正しい場所にのみ追加されます。</span><span class="sxs-lookup"><span data-stu-id="13ec7-267">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="13ec7-268">手動のレンダーツリービルダーのロジックが正しくないと、クラッシュ、サーバーのハング、セキュリティの脆弱性など、任意の未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13ec7-268">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="13ec7-269">アセンブリコードまたは MSIL 命令を手作業で記述するのと同じレベルの複雑さと同じレベルの*危険*を考慮して、手動のレンダリングツリービルダーロジックを検討してください。</span><span class="sxs-lookup"><span data-stu-id="13ec7-269">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
