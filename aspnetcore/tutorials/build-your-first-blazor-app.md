---
title: 最初の Blazor アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: cc7caa1ee01e0282024895ab35c5b9933b1504d0
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416173"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="5d57c-103">最初の Blazor アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="5d57c-103">Build your first Blazor app</span></span>

<span data-ttu-id="5d57c-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5d57c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="5d57c-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="5d57c-106"><xref:blazor/get-started> の記事にあるガイダンスに従って、このチュートリアルの Blazor プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="5d57c-107">プロジェクトに *ToDoList* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="5d57c-108">コンポーネントを構築する</span><span class="sxs-lookup"><span data-stu-id="5d57c-108">Build components</span></span>

1. <span data-ttu-id="5d57c-109">*Pages* フォルダー内にあるアプリの 3 つのページそれぞれを参照します。ホーム、カウンター、データのフェッチです。</span><span class="sxs-lookup"><span data-stu-id="5d57c-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="5d57c-110">これらのページは Razor コンポーネント ファイル *Index.razor*、*Counter.razor*、および *FetchData.razor* によって実装されています。</span><span class="sxs-lookup"><span data-stu-id="5d57c-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="5d57c-111">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="5d57c-112">Web ページでカウンターをインクリメントする場合、通常は JavaScript を記述することが必要です。</span><span class="sxs-lookup"><span data-stu-id="5d57c-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="5d57c-113">Blazor を使用すると、代わりに C# を記述できます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="5d57c-114">*Counter.razor* ファイルで `Counter` コンポーネントの実装を調べます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="5d57c-115">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="5d57c-115">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="5d57c-116">`Counter` コンポーネントの UI は、HTML を使って定義されています。</span><span class="sxs-lookup"><span data-stu-id="5d57c-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="5d57c-117">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="5d57c-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="5d57c-118">HTML マークアップと C# のレンダリング ロジックは、ビルド時にコンポーネント クラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="5d57c-119">生成される .NET クラスの名前はファイル名と同じです。</span><span class="sxs-lookup"><span data-stu-id="5d57c-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="5d57c-120">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="5d57c-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="5d57c-121">`@code` ブロック内では、イベント処理や他のコンポーネント ロジックの定義のために、コンポーネントの状態 (プロパティ、フィールド) とメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="5d57c-122">これらのメンバーは、コンポーネントのレンダリング ロジックの一部として、またイベントを処理するために使われます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="5d57c-123">**[クリックしてください]** ボタンを選択すると:</span><span class="sxs-lookup"><span data-stu-id="5d57c-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="5d57c-124">`Counter` コンポーネントの登録済みの `onclick` ハンドラーが呼び出されます (`IncrementCount` メソッドです)。</span><span class="sxs-lookup"><span data-stu-id="5d57c-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="5d57c-125">`Counter` コンポーネントによりそのレンダリング ツリーが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="5d57c-126">新しいレンダリング ツリーが以前のものと比較されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="5d57c-127">ドキュメント オブジェクト モデル (DOM) に対する変更のみが適用されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="5d57c-128">表示されている数が更新されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="5d57c-129">`Counter` コンポーネントの C# のロジックを変更して、カウントが 1 ではなく 2 ずつインクリメントされるようにします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="5d57c-130">アプリをリビルドして実行し、変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="5d57c-131">**[クリックしてください]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-131">Select the **Click me** button.</span></span> <span data-ttu-id="5d57c-132">カウンターは、2 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="5d57c-133">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="5d57c-133">Use components</span></span>

<span data-ttu-id="5d57c-134">HTML 構文を使用して、別のコンポーネント内にコンポーネントを含めます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="5d57c-135">`Index` コンポーネント (*Index.razor*) に `<Counter />` 要素を追加することで、アプリの `Index` コンポーネントに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="5d57c-136">このエクスペリエンスのために Blazor WebAssembly を使用している場合、`Index` コンポーネントによって `SurveyPrompt` コンポーネントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="5d57c-137">`<SurveyPrompt>` 要素を `<Counter />` 要素に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="5d57c-138">このエクスペリエンスに Blazor サーバー アプリを使用している場合は、`<Counter />` 要素を `Index` コンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="5d57c-139">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="5d57c-139">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="5d57c-140">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-140">Rebuild and run the app.</span></span> <span data-ttu-id="5d57c-141">`Index` コンポーネントには、固有のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="5d57c-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="5d57c-142">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="5d57c-142">Component parameters</span></span>

<span data-ttu-id="5d57c-143">コンポーネントにパラメーターを持たせることもできます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-143">Components can also have parameters.</span></span> <span data-ttu-id="5d57c-144">コンポーネントのパラメーターは、`[Parameter]` 属性が指定されたコンポーネント クラス上で、パブリック プロパティを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="5d57c-145">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="5d57c-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="5d57c-146">コンポーネントの `@code` に関する C# コードを更新します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-146">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="5d57c-147">属性 `[Parameter]` を使用してパブリック `IncrementAmount` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="5d57c-148">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-148">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="5d57c-149">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="5d57c-149">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="5d57c-150">属性を使って、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="5d57c-151">カウンターを 10 ずつインクリメントするように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="5d57c-152">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="5d57c-152">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="5d57c-153">`Index` コンポーネントを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-153">Reload the `Index` component.</span></span> <span data-ttu-id="5d57c-154">カウンターは、 **[クリックしてください]** ボタンを選択するたびに 10 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="5d57c-155">`Counter` コンポーネントにあるカウンターは、継続して 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="5d57c-156">コンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="5d57c-156">Route to components</span></span>

<span data-ttu-id="5d57c-157">*Counter.razor* ファイルの上部にある `@page` ディレクティブは、`Counter` コンポーネントがルーティング エンドポイントであることを指定しています。</span><span class="sxs-lookup"><span data-stu-id="5d57c-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="5d57c-158">`Counter` コンポーネントによって `/counter` に送信された要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="5d57c-159">`@page` ディレクティブがない場合、ルーティングされた要求はコンポーネントでは処理されませんが、そのコンポーネントは他のコンポーネントから引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="5d57c-160">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="5d57c-160">Dependency injection</span></span>

### <a name="blazor-server-experience"></a><span data-ttu-id="5d57c-161">Blazor サーバーのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="5d57c-161">Blazor Server experience</span></span>

<span data-ttu-id="5d57c-162">Blazor サーバー アプリを使用している場合、`WeatherForecastService` サービスは `Startup.ConfigureServices` の[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)として登録されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5d57c-163">サービスのインスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を介してアプリ全体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="5d57c-164">`FetchData` コンポーネントに `WeatherForecastService` サービスのインスタンスを挿入するために、`@inject` ディレクティブが使われています。</span><span class="sxs-lookup"><span data-stu-id="5d57c-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="5d57c-165">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="5d57c-165">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="5d57c-166">`FetchData` コンポーネントでは、`ForecastService` のような挿入されたサービスを使って、`WeatherForecast` オブジェクトの配列を取得します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a><span data-ttu-id="5d57c-167">Blazor WebAssembly のエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="5d57c-167">Blazor WebAssembly experience</span></span>

<span data-ttu-id="5d57c-168">Blazor WebAssembly アプリを使用する場合、*wwwroot/sample-data* フォルダー内の *weather.json* ファイルから天気予報データを取得するために、`HttpClient` が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="5d57c-169">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="5d57c-169">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="5d57c-170">各 forecast 変数を気象データのテーブルの行としてレンダリングするために、[@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ループが使われています。</span><span class="sxs-lookup"><span data-stu-id="5d57c-170">An [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="5d57c-171">Todo リストを構築する</span><span class="sxs-lookup"><span data-stu-id="5d57c-171">Build a todo list</span></span>

<span data-ttu-id="5d57c-172">シンプルな ToDo リストを実装したアプリに新しいコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="5d57c-173">*Pages* フォルダー内のアプリに、*Todo.razor* という名前の空のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-173">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="5d57c-174">コンポーネントに最初のマークアップを指定します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-174">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="5d57c-175">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-175">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="5d57c-176">アプリのレイアウトでは、`NavMenu` コンポーネント (*Shared/NavMenu.csthml*) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-176">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="5d57c-177">レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="5d57c-177">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="5d57c-178">*Shared/NavMenu.csthml* ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加して、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-178">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="5d57c-179">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-179">Rebuild and run the app.</span></span> <span data-ttu-id="5d57c-180">新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-180">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="5d57c-181">Todo アイテムを表すクラスを保持するために、プロジェクトのルートに *TodoItem.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-181">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="5d57c-182">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="5d57c-182">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="5d57c-183">`Todo` コンポーネントに戻ります (*Pages/Todo.razor*)。</span><span class="sxs-lookup"><span data-stu-id="5d57c-183">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="5d57c-184">Todo 項目用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-184">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="5d57c-185">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-185">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="5d57c-186">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-186">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="5d57c-187">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="5d57c-187">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="5d57c-188">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-188">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="5d57c-189">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-189">Rebuild and run the app.</span></span> <span data-ttu-id="5d57c-190">**[Add todo]\(ToDo の追加\)** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。</span><span class="sxs-lookup"><span data-stu-id="5d57c-190">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="5d57c-191">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-191">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="5d57c-192">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-192">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="5d57c-193">新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-193">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="5d57c-194">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-194">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="5d57c-195">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-195">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="5d57c-196">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-196">Rebuild and run the app.</span></span> <span data-ttu-id="5d57c-197">Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-197">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="5d57c-198">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="5d57c-198">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="5d57c-199">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-199">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="5d57c-200">`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-200">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="5d57c-201">それらの値がバインドされていることを確認するために、`<h1>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-201">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="5d57c-202">完成した `Todo` コンポーネント (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="5d57c-202">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="5d57c-203">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5d57c-203">Rebuild and run the app.</span></span> <span data-ttu-id="5d57c-204">Todo アイテムを追加して新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="5d57c-204">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
