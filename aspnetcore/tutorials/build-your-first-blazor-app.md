---
title: 最初の Blazor アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: b433d793ae615bc4ece7c63bebd72d349adf43ee
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081263"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="d45ed-103">最初の Blazor アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="d45ed-103">Build your first Blazor app</span></span>

<span data-ttu-id="d45ed-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d45ed-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d45ed-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="d45ed-106"><xref:blazor/get-started> の記事にあるガイダンスに従って、このチュートリアルの Blazor プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="d45ed-107">プロジェクトに *ToDoList* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="d45ed-108">コンポーネントを構築する</span><span class="sxs-lookup"><span data-stu-id="d45ed-108">Build components</span></span>

1. <span data-ttu-id="d45ed-109">*Pages* フォルダー内にあるアプリの 3 つのページそれぞれを参照します。ホーム、カウンター、データのフェッチです。</span><span class="sxs-lookup"><span data-stu-id="d45ed-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="d45ed-110">これらのページは Razor コンポーネント ファイル *Index.razor*、*Counter.razor*、および *FetchData.razor* によって実装されています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="d45ed-111">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="d45ed-112">Web ページでカウンターをインクリメントする場合、通常は JavaScript を記述することが必要ですが、Blazor には C# を使ったより優れた方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-112">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="d45ed-113">*Counter.razor* ファイルで `Counter` コンポーネントの実装を調べます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-113">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="d45ed-114">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d45ed-114">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="d45ed-115">`Counter` コンポーネントの UI は、HTML を使って定義されています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-115">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="d45ed-116">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="d45ed-117">HTML マークアップと C# のレンダリング ロジックは、ビルド時にコンポーネント クラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-117">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="d45ed-118">生成される .NET クラスの名前はファイル名と同じです。</span><span class="sxs-lookup"><span data-stu-id="d45ed-118">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="d45ed-119">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-119">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="d45ed-120">`@code` ブロック内では、イベント処理や他のコンポーネント ロジックの定義のために、コンポーネントの状態 (プロパティ、フィールド) とメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-120">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="d45ed-121">これらのメンバーは、コンポーネントのレンダリング ロジックの一部として、またイベントを処理するために使われます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-121">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="d45ed-122">**[クリックしてください]** ボタンを選択すると:</span><span class="sxs-lookup"><span data-stu-id="d45ed-122">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="d45ed-123">`Counter` コンポーネントの登録済みの `onclick` ハンドラーが呼び出されます (`IncrementCount` メソッドです)。</span><span class="sxs-lookup"><span data-stu-id="d45ed-123">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="d45ed-124">`Counter` コンポーネントによりそのレンダリング ツリーが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-124">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="d45ed-125">新しいレンダリング ツリーが以前のものと比較されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-125">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="d45ed-126">ドキュメント オブジェクト モデル (DOM) に対する変更のみが適用されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-126">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="d45ed-127">表示されている数が更新されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-127">The displayed count is updated.</span></span>

1. <span data-ttu-id="d45ed-128">`Counter` コンポーネントの C# のロジックを変更して、カウントが 1 ではなく 2 ずつインクリメントされるようにします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-128">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="d45ed-129">アプリをリビルドして実行し、変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-129">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="d45ed-130">**[クリックしてください]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-130">Select the **Click me** button.</span></span> <span data-ttu-id="d45ed-131">カウンターは、2 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-131">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="d45ed-132">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="d45ed-132">Use components</span></span>

<span data-ttu-id="d45ed-133">HTML 構文を使用して、別のコンポーネント内にコンポーネントを含めます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-133">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="d45ed-134">`Index` コンポーネント (*Index.razor*) に `<Counter />` 要素を追加することで、アプリの `Index` コンポーネントに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-134">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="d45ed-135">このエクスペリエンスのために Blazor WebAssembly を使用している場合、`Index` コンポーネントによって `SurveyPrompt` コンポーネントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-135">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="d45ed-136">`<SurveyPrompt>` 要素を `<Counter />` 要素に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-136">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="d45ed-137">このエクスペリエンスに Blazor サーバー アプリを使用している場合は、`<Counter />` 要素を `Index` コンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-137">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="d45ed-138">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d45ed-138">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="d45ed-139">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-139">Rebuild and run the app.</span></span> <span data-ttu-id="d45ed-140">`Index` コンポーネントには、固有のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="d45ed-140">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="d45ed-141">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="d45ed-141">Component parameters</span></span>

<span data-ttu-id="d45ed-142">コンポーネントにパラメーターを持たせることもできます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-142">Components can also have parameters.</span></span> <span data-ttu-id="d45ed-143">コンポーネントのパラメーターは、`[Parameter]` 属性が指定されたコンポーネント クラス上で、パブリック プロパティを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-143">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="d45ed-144">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="d45ed-144">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="d45ed-145">コンポーネントの `@code` に関する C# コードを更新します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-145">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="d45ed-146">属性 `[Parameter]` を使用してパブリック `IncrementAmount` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-146">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="d45ed-147">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-147">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="d45ed-148">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d45ed-148">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="d45ed-149">属性を使って、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-149">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="d45ed-150">カウンターを 10 ずつインクリメントするように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-150">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="d45ed-151">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d45ed-151">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="d45ed-152">`Index` コンポーネントを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-152">Reload the `Index` component.</span></span> <span data-ttu-id="d45ed-153">カウンターは、 **[クリックしてください]** ボタンを選択するたびに 10 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-153">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="d45ed-154">`Counter` コンポーネントにあるカウンターは、継続して 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-154">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="d45ed-155">コンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="d45ed-155">Route to components</span></span>

<span data-ttu-id="d45ed-156">*Counter.razor* ファイルの上部にある `@page` ディレクティブは、`Counter` コンポーネントがルーティング エンドポイントであることを指定しています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-156">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="d45ed-157">`Counter` コンポーネントによって `/counter` に送信された要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-157">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="d45ed-158">`@page` ディレクティブがない場合、ルーティングされた要求はコンポーネントでは処理されませんが、そのコンポーネントは他のコンポーネントから引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-158">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="d45ed-159">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="d45ed-159">Dependency injection</span></span>

<span data-ttu-id="d45ed-160">Blazor サーバー アプリを使用している場合、`WeatherForecastService` サービスは `Startup.ConfigureServices` の[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)として登録されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-160">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d45ed-161">サービスのインスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を介してアプリ全体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-161">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="d45ed-162">`FetchData` コンポーネントに `WeatherForecastService` サービスのインスタンスを挿入するために、`@inject` ディレクティブが使われています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-162">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="d45ed-163">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="d45ed-163">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="d45ed-164">`FetchData` コンポーネントでは、`ForecastService` のような挿入されたサービスを使って、`WeatherForecast` オブジェクトの配列を取得します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-164">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="d45ed-165">Blazor WebAssembly アプリを使用する場合、*wwwroot/sample-data* フォルダー内の *weather.json* ファイルから天気予報データを取得するために、`HttpClient` が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-165">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="d45ed-166">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="d45ed-166">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="d45ed-167">各 forecast インスタンスを気象データのテーブルの行としてレンダリングするために、[\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ループが使われています。</span><span class="sxs-lookup"><span data-stu-id="d45ed-167">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="d45ed-168">Todo リストを構築する</span><span class="sxs-lookup"><span data-stu-id="d45ed-168">Build a todo list</span></span>

<span data-ttu-id="d45ed-169">シンプルな ToDo リストを実装したアプリに新しいコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-169">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="d45ed-170">*Pages* フォルダー内のアプリに、*Todo.razor* という名前の空のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-170">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="d45ed-171">コンポーネントに最初のマークアップを指定します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-171">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="d45ed-172">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-172">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="d45ed-173">アプリのレイアウトでは、`NavMenu` コンポーネント (*Shared/NavMenu.csthml*) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-173">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="d45ed-174">レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="d45ed-174">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="d45ed-175">*Shared/NavMenu.csthml* ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加して、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-175">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="d45ed-176">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-176">Rebuild and run the app.</span></span> <span data-ttu-id="d45ed-177">新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-177">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="d45ed-178">Todo アイテムを表すクラスを保持するために、プロジェクトのルートに *TodoItem.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-178">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="d45ed-179">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="d45ed-179">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="d45ed-180">`Todo` コンポーネントに戻ります (*Pages/Todo.razor*)。</span><span class="sxs-lookup"><span data-stu-id="d45ed-180">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="d45ed-181">Todo 項目用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-181">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="d45ed-182">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-182">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="d45ed-183">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-183">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="d45ed-184">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="d45ed-184">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="d45ed-185">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-185">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="d45ed-186">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-186">Rebuild and run the app.</span></span> <span data-ttu-id="d45ed-187">**[Add todo]\(ToDo の追加\)** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。</span><span class="sxs-lookup"><span data-stu-id="d45ed-187">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="d45ed-188">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-188">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="d45ed-189">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-189">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="d45ed-190">新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-190">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="d45ed-191">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-191">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="d45ed-192">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-192">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="d45ed-193">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-193">Rebuild and run the app.</span></span> <span data-ttu-id="d45ed-194">Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-194">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="d45ed-195">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d45ed-195">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="d45ed-196">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-196">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="d45ed-197">`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-197">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="d45ed-198">それらの値がバインドされていることを確認するために、`<h1>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-198">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="d45ed-199">完成した `Todo` コンポーネント (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="d45ed-199">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="d45ed-200">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="d45ed-200">Rebuild and run the app.</span></span> <span data-ttu-id="d45ed-201">Todo アイテムを追加して新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="d45ed-201">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
