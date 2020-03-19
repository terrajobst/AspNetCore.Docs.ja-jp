---
title: ASP.NET Core Razor コンポーネントの作成と使用
author: guardrex
description: データへのバインド、イベントの処理、コンポーネント ライフサイクルの管理の方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 7afc9250cdfb4b791ef939ead0f41b503d83fad8
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511276"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="336ac-103">ASP.NET Core Razor コンポーネントの作成と使用</span><span class="sxs-lookup"><span data-stu-id="336ac-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="336ac-104">作成者: [Luke Latham](https://github.com/guardrex) と [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="336ac-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="336ac-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="336ac-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="336ac-106"> アプリは *コンポーネント*を使用してビルドします。</span><span class="sxs-lookup"><span data-stu-id="336ac-106"> apps are built using *components*.</span></span> <span data-ttu-id="336ac-107">コンポーネントは、ページ、ダイアログ、フォームなどのユーザー インターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="336ac-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="336ac-108">コンポーネントには、データの挿入や UI イベントへの応答に必要な HTML マークアップと、処理ロジックが含まれます。</span><span class="sxs-lookup"><span data-stu-id="336ac-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="336ac-109">コンポーネントは、柔軟性があり、軽量です。</span><span class="sxs-lookup"><span data-stu-id="336ac-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="336ac-110">それらを入れ子にしたり、再利用したり、プロジェクト間で共有したりできます。</span><span class="sxs-lookup"><span data-stu-id="336ac-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="336ac-111">コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="336ac-111">Component classes</span></span>

<span data-ttu-id="336ac-112">コンポーネントは、C# と HTML マークアップの組み合わせを使用して、[Razor](xref:mvc/views/razor) コンポーネント ファイル ( *.razor*) で実装します。</span><span class="sxs-lookup"><span data-stu-id="336ac-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="336ac-113">Blazor のコンポーネントは、正式には *Razor コンポーネント* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="336ac-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="336ac-114">コンポーネントの名前は、大文字で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="336ac-115">たとえば、*MyCoolComponent.razor* は有効で、*myCoolComponent.razor* は無効です。</span><span class="sxs-lookup"><span data-stu-id="336ac-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="336ac-116">コンポーネントの UI は、HTML を使用して定義します。</span><span class="sxs-lookup"><span data-stu-id="336ac-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="336ac-117">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="336ac-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="336ac-118">アプリがコンパイルされると、HTML マークアップと C# のレンダリング ロジックはコンポーネント クラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="336ac-119">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="336ac-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="336ac-120">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="336ac-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="336ac-121">`@code` ブロックには、イベント処理のメソッド、またはその他のコンポーネント ロジックを定義するためのメソッドによって、コンポーネントの状態 (プロパティ、フィールド) を指定します。</span><span class="sxs-lookup"><span data-stu-id="336ac-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="336ac-122">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="336ac-123">コンポーネント メンバーは、`@` で始まる C# 式を使用して、コンポーネントのレンダリング ロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="336ac-124">たとえば、フィールド名の前に `@` を付けることによって、C# フィールドがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="336ac-125">次の例では、以下のように評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="336ac-126">`_headingFontStyle` が `font-style` の CSS プロパティ値に。</span><span class="sxs-lookup"><span data-stu-id="336ac-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="336ac-127">`_headingText` が `<h1>` 要素のコンテンツに。</span><span class="sxs-lookup"><span data-stu-id="336ac-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="336ac-128">コンポーネントが最初にレンダリングされた後に、コンポーネントがイベントに応答して、レンダリング ツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="336ac-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="336ac-129"> によって新旧のレンダリング ツリーが比較され、ブラウザーのドキュメント オブジェクト モデル (DOM) に変更が適用されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="336ac-130">コンポーネントは通常の C# クラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="336ac-131">Web ページを生成するコンポーネントは、通常、*Pages* フォルダーに存在します。</span><span class="sxs-lookup"><span data-stu-id="336ac-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="336ac-132">ページ以外のコンポーネントは、多くの場合に、*Shared* フォルダー、またはプロジェクトに追加されたカスタム フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="336ac-133">一般に、コンポーネントの名前空間は、アプリのルート名前空間と、アプリ内のコンポーネントの場所 (フォルダー) から派生します。</span><span class="sxs-lookup"><span data-stu-id="336ac-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="336ac-134">アプリのルート名前空間が `BlazorApp` で、`Counter` コンポーネントが *Pages* フォルダーに存在する場合:</span><span class="sxs-lookup"><span data-stu-id="336ac-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="336ac-135">`Counter` コンポーネントの名前空間は `BlazorApp.Pages` になります。</span><span class="sxs-lookup"><span data-stu-id="336ac-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="336ac-136">コンポーネントの完全修飾型名は `BlazorApp.Pages.Counter` になります。</span><span class="sxs-lookup"><span data-stu-id="336ac-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="336ac-137">詳細については、「[コンポーネントのインポート](#import-components)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="336ac-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="336ac-138">カスタム フォルダーを使用するには、カスタム フォルダーの名前空間を親コンポーネントまたはアプリの *_Imports.razor* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="336ac-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="336ac-139">たとえば、アプリのルート名前空間が `BlazorApp` である場合、次の名前空間によって、*Components* フォルダー内のコンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="336ac-140">静的な資産</span><span class="sxs-lookup"><span data-stu-id="336ac-140">Static assets</span></span>

Blazor<span data-ttu-id="336ac-141"> は、プロジェクトの [Web ルート (wwwroot) フォルダー](xref:fundamentals/index#web-root)に静的アセットを配置する ASP.NET Core アプリの規則に従います。</span><span class="sxs-lookup"><span data-stu-id="336ac-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="336ac-142">静的アセットの Web ルートを参照するには、ベース相対パス (`/`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="336ac-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="336ac-143">次の例では、*logo.png* が物理的に *{PROJECT ROOT}/wwwroot/images* フォルダーに置かれています。</span><span class="sxs-lookup"><span data-stu-id="336ac-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="336ac-144">Razor コンポーネントでは、チルダ スラッシュ表記 (`~/`) はサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="336ac-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="336ac-145">アプリのベース パスの設定の詳細については、「<xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="336ac-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="336ac-146">タグ ヘルパーはコンポーネントでサポートされない</span><span class="sxs-lookup"><span data-stu-id="336ac-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="336ac-147">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro) は、Razor コンポーネント ( *.razor* ファイル) でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="336ac-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="336ac-148">Blazor にタグ ヘルパーのような機能を提供するには、タグ ヘルパーと同じ機能を持つコンポーネントを作成し、代わりにそのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="336ac-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="336ac-149">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="336ac-149">Use components</span></span>

<span data-ttu-id="336ac-150">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="336ac-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="336ac-151">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="336ac-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="336ac-152">*Index.razor* の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="336ac-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="336ac-153">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="336ac-154">コンポーネント名と一致しない最初の文字が大文字の HTML 要素がコンポーネントに含まれている場合、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="336ac-155">コンポーネントの名前空間に `@using` ディレクティブを追加すると、コンポーネントを使用できるようになり、警告が解決されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="336ac-156">ルーティング</span><span class="sxs-lookup"><span data-stu-id="336ac-156">Routing</span></span>

<span data-ttu-id="336ac-157">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントへのルート テンプレートを提供することで実現します。</span><span class="sxs-lookup"><span data-stu-id="336ac-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="336ac-158">`@page` ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="336ac-159">実行時に、ルーターによって `RouteAttribute` を持つコンポーネント クラスが検索され、要求された URL に一致するルート テンプレートを使用するコンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="336ac-160">詳細については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="336ac-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="336ac-161">パラメーター</span><span class="sxs-lookup"><span data-stu-id="336ac-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="336ac-162">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="336ac-162">Route parameters</span></span>

<span data-ttu-id="336ac-163">コンポーネントでは、`@page` ディレクティブに指定されたルート テンプレートからルート パラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="336ac-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="336ac-164">ルーターでは、ルート パラメーターを使用して、対応するコンポーネント パラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="336ac-165">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="336ac-166">オプションのパラメーターはサポートされていないため、前の例では 2 つの `@page` ディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="336ac-167">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="336ac-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="336ac-168">2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="336ac-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="336ac-169">複数のフォルダー境界をまたいだパスをキャプチャする*キャッチオール* パラメーター構文 (`*`/`**`) は、Razor コンポーネント ( *.razor*) ではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="336ac-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="336ac-170">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="336ac-170">Component parameters</span></span>

<span data-ttu-id="336ac-171">コンポーネントには、*コンポーネント パラメーター*を指定でき、これは `[Parameter]` 属性を指定したコンポーネント クラスで、パブリック プロパティを使用して定義します。</span><span class="sxs-lookup"><span data-stu-id="336ac-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="336ac-172">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="336ac-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="336ac-173">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="336ac-174">サンプル アプリの次の例では、`ParentComponent` によって `ChildComponent` の `Title` プロパティの値を設定しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="336ac-175">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="336ac-176">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="336ac-176">Child content</span></span>

<span data-ttu-id="336ac-177">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-177">Components can set the content of another component.</span></span> <span data-ttu-id="336ac-178">割り当てコンポーネントでは、受信コンポーネントを指定するタグ間にコンテンツを指定します。</span><span class="sxs-lookup"><span data-stu-id="336ac-178">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="336ac-179">次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す `RenderFragment` を表す `ChildContent` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="336ac-179">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="336ac-180">コンテンツをレンダリングする必要があるコンポーネントのマークアップに、`ChildContent` の値を配置します。</span><span class="sxs-lookup"><span data-stu-id="336ac-180">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="336ac-181">`ChildContent` の値は、親コンポーネントから受け取られ、ブートストラップ パネルの `panel-body` 内にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-181">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="336ac-182">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-182">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="336ac-183">`RenderFragment` コンテンツを受け取るプロパティは、規則によって `ChildContent` という名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-183">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="336ac-184">サンプル アプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` をレンダリングするためのコンテンツを提供できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-184">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="336ac-185">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-185">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="336ac-186">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="336ac-186">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="336ac-187">コンポーネントでは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャしてレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="336ac-187">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="336ac-188">追加の属性は、ディクショナリにキャプチャし、[`@attributes`](xref:mvc/views/razor#attributes) Razor ディレクティブを使用して、コンポーネントがレンダリングされるときに、要素に*スプラッティング*できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-188">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="336ac-189">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="336ac-189">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="336ac-190">たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-190">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="336ac-191">次の例で、最初の `<input>` 要素 (`id="useIndividualParams"`) では、個々のコンポーネント パラメーターを使用していますが、2 番目の `<input>` 要素 (`id="useAttributesDict"`) では、属性スプラッティングを使用しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-191">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="336ac-192">パラメーターの型は、文字列キーで `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-192">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="336ac-193">このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="336ac-193">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="336ac-194">両方の方法を使用してレンダリングされる `<input>` 要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="336ac-194">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="336ac-195">任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true` に設定した `[Parameter]` 属性を使用して、コンポーネント パラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="336ac-195">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="336ac-196">`[Parameter]` の `CaptureUnmatchedValues` プロパティにより、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="336ac-196">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="336ac-197">1 つのコンポーネントで、`CaptureUnmatchedValues` を持つパラメーターは 1 つだけ定義できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-197">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="336ac-198">`CaptureUnmatchedValues` で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-198">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="336ac-199">このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` も使用できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-199">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="336ac-200">要素属性の位置を基準とした `@attributes` の位置は重要です。</span><span class="sxs-lookup"><span data-stu-id="336ac-200">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="336ac-201">`@attributes` が要素にスプラッティングされると、属性は右から左 (最後から最初) に処理されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-201">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="336ac-202">`Child` コンポーネントを使用する次のコンポーネントの例を考えます。</span><span class="sxs-lookup"><span data-stu-id="336ac-202">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="336ac-203">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-203">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="336ac-204">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-204">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="336ac-205">`Child` コンポーネントの `extra` 属性が `@attributes` の右側に設定されています。</span><span class="sxs-lookup"><span data-stu-id="336ac-205">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="336ac-206">属性は右から左 (最後から最初) に処理されるため、追加の属性によって渡された場合に、`Parent` コンポーネントのレンダリングされる `<div>` に、`extra="5"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="336ac-206">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="336ac-207">次の例では、`Child` コンポーネントの `<div>` で、`extra` と `@attributes` の順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="336ac-207">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="336ac-208">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-208">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="336ac-209">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-209">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="336ac-210">追加の属性によって渡された場合に、`Parent` コンポーネント内のレンダリングされる `<div>` には、`extra="10"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="336ac-210">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="336ac-211">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="336ac-211">Capture references to components</span></span>

<span data-ttu-id="336ac-212">コンポーネント参照によって、コンポーネント インスタンスを参照する方法が得られるため、そのインスタンスに `Show` や `Reset` などのコマンドを発行できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-212">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="336ac-213">コンポーネント参照をキャプチャするには:</span><span class="sxs-lookup"><span data-stu-id="336ac-213">To capture a component reference:</span></span>

* <span data-ttu-id="336ac-214">子コンポーネントに [`@ref`](xref:mvc/views/razor#ref) 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="336ac-214">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="336ac-215">子コンポーネントと同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="336ac-215">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="336ac-216">コンポーネントがレンダリングされると、`_loginDialog` フィールドに `MyLoginDialog` 子コンポーネント インスタンスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-216">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="336ac-217">これにより、コンポーネント インスタンスに対し、.NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="336ac-217">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="336ac-218">`_loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="336ac-218">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="336ac-219">この時点まで、何も参照できません。</span><span class="sxs-lookup"><span data-stu-id="336ac-219">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="336ac-220">コンポーネントのレンダリングが完了した後にコンポーネント参照を操作するには、[OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](xref:blazor/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="336ac-220">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="336ac-221">コンポーネント参照のキャプチャでは、[要素参照のキャプチャ](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)と類似の構文を使用しますが、それは JavaScript 相互運用機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="336ac-221">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="336ac-222">コンポーネント参照は、JavaScript コードに渡されません &mdash; それらは .NET コードでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-222">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="336ac-223">子コンポーネントの状態を変えるためにコンポーネント参照を使用**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="336ac-223">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="336ac-224">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="336ac-224">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="336ac-225">通常の宣言型パラメーターを使用すると、子コンポーネントが正しいタイミングで自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-225">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="336ac-226">状態を更新するために外部でコンポーネント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="336ac-226">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="336ac-227"> では、`SynchronizationContext` を使用して、1 つの論理スレッドを強制的に実行します。</span><span class="sxs-lookup"><span data-stu-id="336ac-227"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="336ac-228">コンポーネントの [ライフサイクル メソッド](xref:blazor/lifecycle)と Blazor によって発生するイベント コールバックは、この `SynchronizationContext` で実行されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-228">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="336ac-229">タイマーやその他の通知などの外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazor の `SynchronizationContext` にディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-229">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="336ac-230">たとえば、リッスンしているコンポーネントに、更新状態を通知できる*通知サービス* を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="336ac-230">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="336ac-231">`NotifierService` をシングルトンとして登録します。</span><span class="sxs-lookup"><span data-stu-id="336ac-231">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="336ac-232">Blazor WebAssembly で、`Program.Main` にサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="336ac-232">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="336ac-233">Blazor サーバーで、`Startup.ConfigureServices` にサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="336ac-233">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="336ac-234">`NotifierService` を使用して、コンポーネントを更新します。</span><span class="sxs-lookup"><span data-stu-id="336ac-234">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="336ac-235">前の例では、`NotifierService` が Blazor の `SynchronizationContext` の外部で、コンポーネントの `OnNotify` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="336ac-235">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="336ac-236">`InvokeAsync` を使用して、正しいコンテキストに切り替え、レンダリングをキューに登録します。</span><span class="sxs-lookup"><span data-stu-id="336ac-236">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="336ac-237">\@ キーを使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="336ac-237">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="336ac-238">要素またはコンポーネントのリストをレンダリングし、その後に要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムでは、前のどの要素やコンポーネントを保持できるか、およびモデル オブジェクトをそれらにどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-238">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="336ac-239">通常、このプロセスは自動で、無視できますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-239">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="336ac-240">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="336ac-240">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="336ac-241">`People` コレクションのコンテンツは、挿入、削除、または順序変更されたエントリによって変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-241">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="336ac-242">コンポーネントのレンダリング時に、`<DetailsEditor>` コンポーネントが変更され、異なる `Details` パラメーター値を受け取ることがあります。</span><span class="sxs-lookup"><span data-stu-id="336ac-242">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="336ac-243">これにより、予期したものよりも複雑な再レンダリングが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-243">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="336ac-244">場合によっては、再レンダリングによって、要素のフォーカスの喪失などの表示動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-244">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="336ac-245">マッピング プロセスは、`@key` ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-245">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="336ac-246">`@key` により、比較アルゴリズムで、キーの値に基づいて要素やコンポーネントが確実に保持されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-246">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="336ac-247">`People` コレクションが変更されても、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンス間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-247">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="336ac-248">`Person` が `People` リストから削除された場合、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-248">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="336ac-249">他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="336ac-249">Other instances are left unchanged.</span></span>
* <span data-ttu-id="336ac-250">リスト内の特定の位置に `Person` が挿入されると、その対応する位置に、1 つの新しい `<DetailsEditor>` インスタンスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-250">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="336ac-251">他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="336ac-251">Other instances are left unchanged.</span></span>
* <span data-ttu-id="336ac-252">`Person` エントリの順序が変更された場合、対応する `<DetailsEditor>` インスタンスは UI で保持され、順序が変更されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-252">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="336ac-253">シナリオによっては、`@key` を使用すると、レンダリングの複雑さが最小限に抑えられ、フォーカス位置など、DOM 変更のステートフルな部分の潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-253">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="336ac-254">キーは、各コンテナー要素やコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="336ac-254">Keys are local to each container element or component.</span></span> <span data-ttu-id="336ac-255">キーはドキュメント全体でグローバルに比較されません。</span><span class="sxs-lookup"><span data-stu-id="336ac-255">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="336ac-256">\@ キーを使用する場面</span><span class="sxs-lookup"><span data-stu-id="336ac-256">When to use \@key</span></span>

<span data-ttu-id="336ac-257">一般に、リストがレンダリングされ (たとえば、`@foreach` ブロックで)、`@key` を定義するための適切な値が存在する場合は常に、`@key` を使用することは意味があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-257">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="336ac-258">また、`@key` を使用して、オブジェクトが変更されたときに Blazor が要素やコンポーネントのサブツリーを保持しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="336ac-258">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="336ac-259">`@currentPerson` が変更された場合、`@key` 属性ディレクティブによって、Blazor に、`<div>` とその子孫全体を破棄させ、新しい要素とコンポーネントで UI 内のサブツリーをリビルドさせます。</span><span class="sxs-lookup"><span data-stu-id="336ac-259">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="336ac-260">これは、`@currentPerson` が変更されたときに、UI の状態が確実に保持されないようにする必要がある場合に役立つ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-260">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="336ac-261">\@ キーを使用しない場面</span><span class="sxs-lookup"><span data-stu-id="336ac-261">When not to use \@key</span></span>

<span data-ttu-id="336ac-262">`@key` で比較すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="336ac-262">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="336ac-263">パフォーマンスの低下は大きくありませんが、要素やコンポーネントの保存規則を制御することによって、アプリにメリットがある場合にのみ `@key` を指定してください。</span><span class="sxs-lookup"><span data-stu-id="336ac-263">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="336ac-264">`@key` を使用しない場合でも、Blazor では可能な限り、子要素とコンポーネント インスタンスが保持されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-264">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="336ac-265">`@key` を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネント インスタンスにモデル インスタンスをマップする*方法*を制御することです。</span><span class="sxs-lookup"><span data-stu-id="336ac-265">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="336ac-266">\@ キーに使用する値</span><span class="sxs-lookup"><span data-stu-id="336ac-266">What values to use for \@key</span></span>

<span data-ttu-id="336ac-267">一般に、`@key` には、次のいずれかの種類の値を指定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="336ac-267">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="336ac-268">モデル オブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。</span><span class="sxs-lookup"><span data-stu-id="336ac-268">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="336ac-269">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-269">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="336ac-270">一意の識別子 (たとえば、`int` 型、`string` 型、`Guid` 型の主キー値)。</span><span class="sxs-lookup"><span data-stu-id="336ac-270">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="336ac-271">`@key` に使用される値は競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="336ac-271">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="336ac-272">同じ親要素内で競合する値が検出された場合、Blazor では、古い要素やコンポーネントを新しい要素やコンポーネントに確定的にマップできないため、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-272">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="336ac-273">個別の値 (オブジェクト インスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="336ac-273">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="336ac-274">部分クラスのサポート</span><span class="sxs-lookup"><span data-stu-id="336ac-274">Partial class support</span></span>

<span data-ttu-id="336ac-275">Razor コンポーネントは、部分クラスとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-275">Razor components are generated as partial classes.</span></span> <span data-ttu-id="336ac-276">Razor コンポーネントは、次のいずれかの方法を使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="336ac-276">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="336ac-277">C# コードは、1 つのファイルに HTML マークアップと Razor コードを含む [`@code`](xref:mvc/views/razor#code) ブロックで定義します。</span><span class="sxs-lookup"><span data-stu-id="336ac-277">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="336ac-278"> テンプレートでは、この方法を使用して Razor コンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="336ac-278"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="336ac-279">C# コードは、部分クラスとして定義されている分離コード ファイルに配置されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-279">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="336ac-280">次の例は、Blazor テンプレートから生成されたアプリ内の `@code` ブロックを含む既定の `Counter` コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-280">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="336ac-281">HTML マークアップ、Razor コード、C# コードは、同じファイル内にあります。</span><span class="sxs-lookup"><span data-stu-id="336ac-281">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="336ac-282">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-282">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="336ac-283">`Counter` コンポーネントは、部分クラスを含む分離コード ファイルを使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="336ac-283">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="336ac-284">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-284">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="336ac-285">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="336ac-285">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="336ac-286">必要に応じて、部分クラスファイルに必要な名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="336ac-286">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="336ac-287">Razor コンポーネントで使用される一般的な名前空間には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="336ac-287">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="336ac-288">基本クラスの指定</span><span class="sxs-lookup"><span data-stu-id="336ac-288">Specify a base class</span></span>

<span data-ttu-id="336ac-289">[`@inherits`](xref:mvc/views/razor#inherits) ディレクティブを使用して、コンポーネントの基本クラスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-289">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="336ac-290">次の例は、コンポーネントが基本クラス `BlazorRocksBase` を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-290">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="336ac-291">基本クラスは `ComponentBase` から派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-291">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="336ac-292">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="336ac-292">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="336ac-293">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="336ac-293">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="336ac-294">属性の指定</span><span class="sxs-lookup"><span data-stu-id="336ac-294">Specify an attribute</span></span>

<span data-ttu-id="336ac-295">属性は、[`@attribute`](xref:mvc/views/razor#attribute) ディレクティブを使用して Razor コンポーネントに指定できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-295">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="336ac-296">次の例では、`[Authorize]` 属性をコンポーネント クラスに適用しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-296">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="336ac-297">コンポーネントのインポート</span><span class="sxs-lookup"><span data-stu-id="336ac-297">Import components</span></span>

<span data-ttu-id="336ac-298">Razor で作成されるコンポーネントの名前空間は、次に基づきます (優先順で)。</span><span class="sxs-lookup"><span data-stu-id="336ac-298">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="336ac-299">Razor ファイル ( *.razor*) マークアップ内の [`@namespace`](xref:mvc/views/razor#namespace) の指定 (`@namespace BlazorSample.MyNamespace`)。</span><span class="sxs-lookup"><span data-stu-id="336ac-299">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="336ac-300">プロジェクト ファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。</span><span class="sxs-lookup"><span data-stu-id="336ac-300">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="336ac-301">プロジェクト ファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクト ルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="336ac-301">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="336ac-302">たとえば、フレームワークでは *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) が名前空間 `BlazorSample.Pages` に解決されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-302">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="336ac-303">コンポーネントは C# の名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="336ac-303">Components follow C# name binding rules.</span></span> <span data-ttu-id="336ac-304">この例の `Index` コンポーネントの場合、スコープ内のコンポーネントは、次のすべてのコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="336ac-304">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="336ac-305">同じ *Pages* フォルダー内。</span><span class="sxs-lookup"><span data-stu-id="336ac-305">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="336ac-306">別の名前空間を明示的に指定しない、プロジェクトのルート内のコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="336ac-306">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="336ac-307">別の名前空間に定義されているコンポーネントは、Razor の [`@using`](xref:mvc/views/razor#using) ディレクティブを使用してスコープ内に取り込みます。</span><span class="sxs-lookup"><span data-stu-id="336ac-307">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="336ac-308">別のコンポーネントの `NavMenu.razor` が *BlazorSample/Shared/* フォルダーに存在する場合は、次の `@using` ステートメントを使用して、そのコンポーネントを `Index.razor` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-308">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="336ac-309">コンポーネントは、完全修飾名を使用して参照することもできます。この場合、[`@using`](xref:mvc/views/razor#using) ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="336ac-309">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="336ac-310">`global::` 修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="336ac-310">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="336ac-311">別名が付けられた `using` ステートメント (`@using Foo = Bar` など) によるコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="336ac-311">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="336ac-312">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="336ac-312">Partially qualified names aren't supported.</span></span> <span data-ttu-id="336ac-313">たとえば、`<Shared.NavMenu></Shared.NavMenu>` による `@using BlazorSample` の追加と `NavMenu.razor` の参照はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="336ac-313">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="336ac-314">条件付き HTML 要素属性</span><span class="sxs-lookup"><span data-stu-id="336ac-314">Conditional HTML element attributes</span></span>

<span data-ttu-id="336ac-315">HTML 要素属性は、.NET 値に基づいて条件付きでレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-315">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="336ac-316">値が `false` または `null` の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="336ac-316">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="336ac-317">値が `true` の場合、属性が最小化されてレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-317">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="336ac-318">次の例では、`IsCompleted` によって `checked` が要素のマークアップにレンダリングされるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="336ac-318">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="336ac-319">`IsCompleted` が `true` の場合、このチェック ボックスは次のようにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-319">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="336ac-320">`IsCompleted` が `false` の場合、このチェック ボックスは次のようにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-320">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="336ac-321">詳細については、「<xref:mvc/views/razor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="336ac-321">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="336ac-322">.NET 型が `bool` の場合、[aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) などの一部の HTML 属性が正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="336ac-322">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="336ac-323">そのような場合は、`bool` ではなく `string` 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="336ac-323">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="336ac-324">生 HTML</span><span class="sxs-lookup"><span data-stu-id="336ac-324">Raw HTML</span></span>

<span data-ttu-id="336ac-325">通常、文字列は DOM テキスト ノードを使用してレンダリングされます。つまり、それらに含まれている可能性のあるすべてのマークアップが無視され、リテラル テキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="336ac-325">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="336ac-326">生 HTML をレンダリングするには、HTML コンテンツを `MarkupString` 値にラップします。</span><span class="sxs-lookup"><span data-stu-id="336ac-326">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="336ac-327">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-327">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="336ac-328">信頼されていないソースから構築された生 HTML をレンダリングすることは、**セキュリティ リスク**であるため、避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-328">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="336ac-329">次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-329">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="336ac-330">値とパラメーターのカスケード</span><span class="sxs-lookup"><span data-stu-id="336ac-330">Cascading values and parameters</span></span>

<span data-ttu-id="336ac-331">一部のシナリオでは、特に複数のコンポーネント レイヤーがある場合に、[コンポーネント パラメーター](#component-parameters)を使用して、先祖コンポーネントから子孫コンポーネントにデータをフローすることが不便な場合があります。</span><span class="sxs-lookup"><span data-stu-id="336ac-331">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="336ac-332">値とパラメーターのカスケードによって、先祖コンポーネントがそのすべての子孫コンポーネントに値を提供するための便利な方法を用意することで、この問題が解決されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-332">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="336ac-333">値とパラメーターのカスケードによって、コンポーネントで調整する方法も得られます。</span><span class="sxs-lookup"><span data-stu-id="336ac-333">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="336ac-334">テーマの例</span><span class="sxs-lookup"><span data-stu-id="336ac-334">Theme example</span></span>

<span data-ttu-id="336ac-335">次のサンプル アプリの例では、`ThemeInfo` クラスで、コンポーネント階層の下位に伝達されるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。</span><span class="sxs-lookup"><span data-stu-id="336ac-335">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="336ac-336">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="336ac-336">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="336ac-337">先祖コンポーネントでは、Cascading Value コンポーネントを使用してカスケード値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-337">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="336ac-338">`CascadingValue` コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="336ac-338">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="336ac-339">たとえば、サンプル アプリでは、アプリのレイアウトの 1 つに、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード パラメーターとして、テーマ情報 (`ThemeInfo`) を指定しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-339">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="336ac-340">`ButtonClass` には、レイアウト コンポーネント内の `btn-success` の値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="336ac-340">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="336ac-341">すべての子孫コンポーネントで、`ThemeInfo` カスケード オブジェクトからこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-341">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="336ac-342">`CascadingValuesParametersLayout` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="336ac-342">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="336ac-343">カスケード値を使用するには、コンポーネントで `[CascadingParameter]` 属性を使用してカスケード パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="336ac-343">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="336ac-344">カスケード値は、型によってカスケード パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-344">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="336ac-345">サンプル アプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値をカスケード パラメーターにバインドしています。</span><span class="sxs-lookup"><span data-stu-id="336ac-345">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="336ac-346">パラメーターは、コンポーネントによって表示されるボタンの 1 つに CSS クラスを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="336ac-346">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="336ac-347">`CascadingValuesParametersTheme` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="336ac-347">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="336ac-348">同じサブツリー内で同じ型の複数の値をカスケードするには、各 `CascadingValue` コンポーネントとその対応する `CascadingParameter` に一意の `Name` 文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="336ac-348">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="336ac-349">次の例では、2 つの `CascadingValue` コンポーネントで、名前によって `MyCascadingType` の異なるインスタンスをカスケードしています。</span><span class="sxs-lookup"><span data-stu-id="336ac-349">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="336ac-350">子孫コンポーネントでは、カスケードされたパラメーターは、それらの値を、名前によって、先祖コンポーネント内の対応するカスケードされた値から受け取ります。</span><span class="sxs-lookup"><span data-stu-id="336ac-350">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="336ac-351">TabSet の例</span><span class="sxs-lookup"><span data-stu-id="336ac-351">TabSet example</span></span>

<span data-ttu-id="336ac-352">パラメーターのカスケードによって、コンポーネント階層全体でコンポーネントを連携させることもできます。</span><span class="sxs-lookup"><span data-stu-id="336ac-352">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="336ac-353">たとえば、サンプル アプリの次の *TabSet* の例を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="336ac-353">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="336ac-354">このサンプル アプリには、タブに実装されている `ITab` インターフェイスがあります。</span><span class="sxs-lookup"><span data-stu-id="336ac-354">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="336ac-355">`CascadingValuesParametersTabSet` コンポーネントでは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="336ac-355">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="336ac-356">子 `Tab` コンポーネントは、パラメーターとして `TabSet` に明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="336ac-356">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="336ac-357">代わりに、子 `Tab` コンポーネントは、`TabSet` の子コンテンツに含まれます。</span><span class="sxs-lookup"><span data-stu-id="336ac-357">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="336ac-358">ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントでは、*それ自体をカスケード値として指定し*、その後に子孫 `Tab` コンポーネントによって取得できるようにします。</span><span class="sxs-lookup"><span data-stu-id="336ac-358">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="336ac-359">`TabSet` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="336ac-359">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="336ac-360">子孫の `Tab` コンポーネントでは、含まれている `TabSet` をカスケード パラメーターとしてキャプチャするため、`Tab` コンポーネントはそれ自体を `TabSet` に追加し、どのタブをアクティブにするかを調整します。</span><span class="sxs-lookup"><span data-stu-id="336ac-360">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="336ac-361">`Tab` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="336ac-361">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="336ac-362">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="336ac-362">Razor templates</span></span>

<span data-ttu-id="336ac-363">レンダリング フラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="336ac-363">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="336ac-364">Razor テンプレートは、UI スニペットを定義する 1 つの方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-364">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="336ac-365">次の例では、`RenderFragment` と `RenderFragment<T>` の値を指定し、コンポーネント内にテンプレートを直接レンダリングする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="336ac-365">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="336ac-366">レンダリング フラグメントは、引数として[テンプレート コンポーネント](xref:blazor/templated-components)に渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="336ac-366">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="336ac-367">前のコードのレンダリングされた結果:</span><span class="sxs-lookup"><span data-stu-id="336ac-367">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="336ac-368">スケーラブル ベクター グラフィックス (SVG) イメージ</span><span class="sxs-lookup"><span data-stu-id="336ac-368">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="336ac-369">Blazor は HTML をレンダリングするため、スケーラブル ベクター グラフィックス (SVG) イメージ ( *.svg*) などのブラウザーでサポートされているイメージは、`<img>` タグを介してサポートされます。</span><span class="sxs-lookup"><span data-stu-id="336ac-369">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="336ac-370">同様に、SVG イメージは、スタイルシート ファイル ( *.css*) の CSS 規則でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="336ac-370">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="336ac-371">ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="336ac-371">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="336ac-372">`<svg>` タグをコンポーネント ファイル ( *.razor*) に直接配置した場合、基本的なイメージ レンダリングはサポートされますが、多くの高度なシナリオはまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="336ac-372">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="336ac-373">たとえば、`<use>` タグは現在考慮されないため、一部の SVG タグで `@bind` を使用できません。</span><span class="sxs-lookup"><span data-stu-id="336ac-373">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="336ac-374">将来のリリースで、これらの制限に対処する予定です。</span><span class="sxs-lookup"><span data-stu-id="336ac-374">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="336ac-375">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="336ac-375">Additional resources</span></span>

* <span data-ttu-id="336ac-376"><xref:security/blazor/server> &ndash; リソース不足に対処する必要がある Blazor サーバー アプリの構築に関するガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="336ac-376"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
