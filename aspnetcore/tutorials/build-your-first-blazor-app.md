---
title: 最初の Blazor アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: ea1111f43b6b8b4f47061056e8ad8d505f92dba6
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800478"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="5940a-103">最初の Blazor アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="5940a-103">Build your first Blazor app</span></span>

<span data-ttu-id="5940a-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5940a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5940a-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5940a-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="5940a-106"><xref:blazor/get-started> の記事にあるガイダンスに従って、このチュートリアルの Blazor プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5940a-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="5940a-107">プロジェクトに *ToDoList* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="5940a-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="5940a-108">コンポーネントを構築する</span><span class="sxs-lookup"><span data-stu-id="5940a-108">Build components</span></span>

1. <span data-ttu-id="5940a-109">*Pages* フォルダー内にあるアプリの 3 つのページそれぞれを参照します。ホーム、カウンター、データのフェッチです。</span><span class="sxs-lookup"><span data-stu-id="5940a-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="5940a-110">これらのページは Razor コンポーネント ファイル *Index.razor*、*Counter.razor*、および *FetchData.razor* によって実装されています。</span><span class="sxs-lookup"><span data-stu-id="5940a-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="5940a-111">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="5940a-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="5940a-112">Web ページでカウンターをインクリメントする場合、通常は JavaScript を記述することが必要ですが、Blazor には C# を使ったより優れた方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="5940a-112">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="5940a-113">*Counter.razor* ファイルで `Counter` コンポーネントの実装を調べます。</span><span class="sxs-lookup"><span data-stu-id="5940a-113">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="5940a-114">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="5940a-114">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="5940a-115">`Counter` コンポーネントの UI は、HTML を使って定義されています。</span><span class="sxs-lookup"><span data-stu-id="5940a-115">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="5940a-116">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="5940a-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="5940a-117">HTML マークアップと C# のレンダリング ロジックは、ビルド時にコンポーネント クラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-117">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="5940a-118">生成される .NET クラスの名前はファイル名と同じです。</span><span class="sxs-lookup"><span data-stu-id="5940a-118">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="5940a-119">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="5940a-119">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="5940a-120">`@code` ブロック内では、イベント処理や他のコンポーネント ロジックの定義のために、コンポーネントの状態 (プロパティ、フィールド) とメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="5940a-120">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="5940a-121">これらのメンバーは、コンポーネントのレンダリング ロジックの一部として、またイベントを処理するために使われます。</span><span class="sxs-lookup"><span data-stu-id="5940a-121">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="5940a-122">**[クリックしてください]** ボタンを選択すると:</span><span class="sxs-lookup"><span data-stu-id="5940a-122">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="5940a-123">`Counter` コンポーネントの登録済みの `onclick` ハンドラーが呼び出されます (`IncrementCount` メソッドです)。</span><span class="sxs-lookup"><span data-stu-id="5940a-123">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="5940a-124">`Counter` コンポーネントによりそのレンダリング ツリーが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-124">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="5940a-125">新しいレンダリング ツリーが以前のものと比較されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-125">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="5940a-126">ドキュメント オブジェクト モデル (DOM) に対する変更のみが適用されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-126">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="5940a-127">表示されている数が更新されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-127">The displayed count is updated.</span></span>

1. <span data-ttu-id="5940a-128">`Counter` コンポーネントの C# のロジックを変更して、カウントが 1 ではなく 2 ずつインクリメントされるようにします。</span><span class="sxs-lookup"><span data-stu-id="5940a-128">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="5940a-129">アプリをリビルドして実行し、変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="5940a-129">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="5940a-130">**[クリックしてください]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5940a-130">Select the **Click me** button.</span></span> <span data-ttu-id="5940a-131">カウンターは、2 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="5940a-131">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="5940a-132">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="5940a-132">Use components</span></span>

<span data-ttu-id="5940a-133">HTML 構文を使用して、別のコンポーネント内にコンポーネントを含めます。</span><span class="sxs-lookup"><span data-stu-id="5940a-133">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="5940a-134">`Index` コンポーネント (*Index.razor*) に `<Counter />` 要素を追加することで、アプリの `Index` コンポーネントに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-134">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="5940a-135">このエクスペリエンスのためにクライアント側 Blazor を使っている場合、`Index` コンポーネントによって `SurveyPrompt` コンポーネントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-135">If you're using Blazor client-side for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="5940a-136">`<SurveyPrompt>` 要素を `<Counter />` 要素に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5940a-136">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="5940a-137">このエクスペリエンスに Blazor サーバー側アプリを使用している場合は、`<Counter />` 要素を `Index` コンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-137">If you're using a Blazor server-side app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="5940a-138">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="5940a-138">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="5940a-139">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5940a-139">Rebuild and run the app.</span></span> <span data-ttu-id="5940a-140">`Index` コンポーネントには、固有のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="5940a-140">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="5940a-141">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="5940a-141">Component parameters</span></span>

<span data-ttu-id="5940a-142">コンポーネントにパラメーターを持たせることもできます。</span><span class="sxs-lookup"><span data-stu-id="5940a-142">Components can also have parameters.</span></span> <span data-ttu-id="5940a-143">コンポーネントのパラメーターは、`[Parameter]` 属性が指定されたコンポーネント クラス上で、パブリック プロパティを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-143">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="5940a-144">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="5940a-144">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="5940a-145">コンポーネントの `@code` に関する C# コードを更新します。</span><span class="sxs-lookup"><span data-stu-id="5940a-145">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="5940a-146">属性 `[Parameter]` を使用してパブリック `IncrementAmount` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-146">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="5940a-147">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="5940a-147">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="5940a-148">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="5940a-148">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="5940a-149">属性を使って、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="5940a-149">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="5940a-150">カウンターを 10 ずつインクリメントするように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="5940a-150">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="5940a-151">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="5940a-151">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="5940a-152">`Index` コンポーネントを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5940a-152">Reload the `Index` component.</span></span> <span data-ttu-id="5940a-153">カウンターは、 **[クリックしてください]** ボタンを選択するたびに 10 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="5940a-153">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="5940a-154">`Counter` コンポーネントにあるカウンターは、継続して 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="5940a-154">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="5940a-155">コンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="5940a-155">Route to components</span></span>

<span data-ttu-id="5940a-156">*Counter.razor* ファイルの上部にある `@page` ディレクティブは、`Counter` コンポーネントがルーティング エンドポイントであることを指定しています。</span><span class="sxs-lookup"><span data-stu-id="5940a-156">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="5940a-157">`Counter` コンポーネントによって `/counter` に送信された要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-157">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="5940a-158">`@page` ディレクティブがない場合、ルーティングされた要求はコンポーネントでは処理されませんが、そのコンポーネントは他のコンポーネントから引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="5940a-158">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="5940a-159">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="5940a-159">Dependency injection</span></span>

<span data-ttu-id="5940a-160">アプリのサービス コンテナーに登録されたサービスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を介してコンポーネントから使用できます。</span><span class="sxs-lookup"><span data-stu-id="5940a-160">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5940a-161">`@inject` ディレクティブを使ってサービスをコンポーネントに挿入します。</span><span class="sxs-lookup"><span data-stu-id="5940a-161">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="5940a-162">`FetchData` コンポーネントのディレクティブを調べます。</span><span class="sxs-lookup"><span data-stu-id="5940a-162">Examine the directives of the `FetchData` component.</span></span>

<span data-ttu-id="5940a-163">サーバー側 Razor アプリを使用する場合、`WeatherForecastService` サービスは[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)として登録されているため、サービスの 1 つのインスタンスをアプリ全体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="5940a-163">If working with a Blazor server-side app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="5940a-164">コンポーネントに `WeatherForecastService` サービスのインスタンスを挿入するために、`@inject` ディレクティブが使われています。</span><span class="sxs-lookup"><span data-stu-id="5940a-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="5940a-165">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="5940a-165">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="5940a-166">`FetchData` コンポーネントでは、`ForecastService` のような挿入されたサービスを使って、`WeatherForecast` オブジェクトの配列を取得します。</span><span class="sxs-lookup"><span data-stu-id="5940a-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="5940a-167">クライアント側 Blazor アプリを使用する場合、*wwwroot/sample-data* フォルダー内の *weather.json* ファイルから天気予報データを取得するために、`HttpClient` が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-167">If working with a Blazor client-side app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="5940a-168">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="5940a-168">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="5940a-169">各 forecast インスタンスを気象データのテーブルの行としてレンダリングするために、[\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ループが使われています。</span><span class="sxs-lookup"><span data-stu-id="5940a-169">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a><span data-ttu-id="5940a-170">Todo リストを構築する</span><span class="sxs-lookup"><span data-stu-id="5940a-170">Build a todo list</span></span>

<span data-ttu-id="5940a-171">シンプルな ToDo リストを実装したアプリに新しいコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-171">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="5940a-172">*Pages* フォルダー内のアプリに、*Todo.razor* という名前の空のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-172">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="5940a-173">コンポーネントに最初のマークアップを指定します。</span><span class="sxs-lookup"><span data-stu-id="5940a-173">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="5940a-174">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-174">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="5940a-175">アプリのレイアウトでは、`NavMenu` コンポーネント (*Shared/NavMenu.csthml*) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-175">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="5940a-176">レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="5940a-176">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="5940a-177">*Shared/NavMenu.csthml* ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加して、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-177">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="5940a-178">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5940a-178">Rebuild and run the app.</span></span> <span data-ttu-id="5940a-179">新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5940a-179">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="5940a-180">Todo アイテムを表すクラスを保持するために、プロジェクトのルートに *TodoItem.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-180">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="5940a-181">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="5940a-181">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="5940a-182">`Todo` コンポーネントに戻ります (*Pages/Todo.razor*)。</span><span class="sxs-lookup"><span data-stu-id="5940a-182">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="5940a-183">Todo 項目用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-183">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="5940a-184">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="5940a-184">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="5940a-185">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-185">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="5940a-186">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="5940a-186">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="5940a-187">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="5940a-187">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="5940a-188">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5940a-188">Rebuild and run the app.</span></span> <span data-ttu-id="5940a-189">**[Add todo]\(ToDo の追加\)** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。</span><span class="sxs-lookup"><span data-stu-id="5940a-189">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="5940a-190">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。</span><span class="sxs-lookup"><span data-stu-id="5940a-190">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="5940a-191">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5940a-191">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="5940a-192">新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。</span><span class="sxs-lookup"><span data-stu-id="5940a-192">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="5940a-193">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="5940a-193">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="5940a-194">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="5940a-194">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="5940a-195">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5940a-195">Rebuild and run the app.</span></span> <span data-ttu-id="5940a-196">Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="5940a-196">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="5940a-197">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="5940a-197">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="5940a-198">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="5940a-198">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="5940a-199">`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="5940a-199">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="5940a-200">それらの値がバインドされていることを確認するために、`<h1>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="5940a-200">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="5940a-201">完成した `Todo` コンポーネント (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="5940a-201">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="5940a-202">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="5940a-202">Rebuild and run the app.</span></span> <span data-ttu-id="5940a-203">Todo アイテムを追加して新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="5940a-203">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
