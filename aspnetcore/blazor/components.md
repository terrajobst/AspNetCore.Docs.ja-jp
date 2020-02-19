---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447101"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="603ae-103">ASP.NET Core Razor コンポーネントを作成して使用する</span><span class="sxs-lookup"><span data-stu-id="603ae-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="603ae-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="603ae-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="603ae-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="603ae-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="603ae-106"> アプリは*コンポーネント*を使用して構築されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-106"> apps are built using *components*.</span></span> <span data-ttu-id="603ae-107">コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="603ae-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="603ae-108">コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="603ae-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="603ae-109">コンポーネントは柔軟で軽量です。</span><span class="sxs-lookup"><span data-stu-id="603ae-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="603ae-110">入れ子にして再利用したり、プロジェクト間で共有したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="603ae-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="603ae-111">コンポーネントクラス</span><span class="sxs-lookup"><span data-stu-id="603ae-111">Component classes</span></span>

<span data-ttu-id="603ae-112">コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="603ae-113">Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="603ae-114">コンポーネントの名前は、大文字で始まる必要があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="603ae-115">たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。</span><span class="sxs-lookup"><span data-stu-id="603ae-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="603ae-116">コンポーネントの UI は、HTML を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="603ae-117">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="603ae-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="603ae-118">アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="603ae-119">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="603ae-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="603ae-120">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="603ae-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="603ae-121">`@code` ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッド、またはその他のコンポーネントロジックを定義するために指定されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="603ae-122">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="603ae-123">コンポーネントメンバーは、`@`で始まる式を使用してC# 、コンポーネントのレンダリングロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="603ae-124">たとえば、 C#フィールド名をプレフィックス `@` によって表示されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="603ae-125">次の例では、が評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="603ae-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="603ae-126">`font-style`の CSS プロパティ値に `_headingFontStyle` します。</span><span class="sxs-lookup"><span data-stu-id="603ae-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="603ae-127">`<h1>` 要素の内容に `_headingText` します。</span><span class="sxs-lookup"><span data-stu-id="603ae-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="603ae-128">コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="603ae-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="603ae-129"> は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="603ae-130">コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="603ae-131">Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="603ae-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="603ae-132">ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="603ae-133">通常、コンポーネントの名前空間は、アプリのルート名前空間と、アプリ内のコンポーネントの場所 (フォルダー) から派生します。</span><span class="sxs-lookup"><span data-stu-id="603ae-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="603ae-134">アプリのルート名前空間が `BlazorApp`、`Counter` コンポーネントが*Pages*フォルダーに存在する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="603ae-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="603ae-135">`Counter` コンポーネントの名前空間が `BlazorApp.Pages`。</span><span class="sxs-lookup"><span data-stu-id="603ae-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="603ae-136">コンポーネントの完全修飾型名が `BlazorApp.Pages.Counter`ます。</span><span class="sxs-lookup"><span data-stu-id="603ae-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="603ae-137">詳細については、「[コンポーネントのインポート](#import-components)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="603ae-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="603ae-138">カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を親コンポーネントまたはアプリの *_Imports razor*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="603ae-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="603ae-139">たとえば、次の名前空間は、アプリのルート名前空間が `BlazorApp`場合*に、コンポーネントフォルダー内*のコンポーネントを使用可能にします。</span><span class="sxs-lookup"><span data-stu-id="603ae-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="tag-helpers-arent-used-in-components"></a><span data-ttu-id="603ae-140">タグヘルパーはコンポーネントで使用されていません</span><span class="sxs-lookup"><span data-stu-id="603ae-140">Tag Helpers aren't used in components</span></span>

<span data-ttu-id="603ae-141">[タグヘルパー](xref:mvc/views/tag-helpers/intro)は razor コンポーネント (*razor*ファイル) ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="603ae-141">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="603ae-142">Blazorにタグヘルパーのような機能を提供するには、タグヘルパーと同じ機能を持つコンポーネントを作成し、代わりにそのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-142">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="603ae-143">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="603ae-143">Use components</span></span>

<span data-ttu-id="603ae-144">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="603ae-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="603ae-145">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="603ae-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="603ae-146">属性のバインドでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-146">Attribute binding is case sensitive.</span></span> <span data-ttu-id="603ae-147">たとえば、`@bind` が有効で、`@Bind` が無効です。</span><span class="sxs-lookup"><span data-stu-id="603ae-147">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="603ae-148">*Index. razor*の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="603ae-148">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="603ae-149">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="603ae-149">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="603ae-150">コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-150">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="603ae-151">コンポーネントの名前空間に `@using` ディレクティブを追加すると、コンポーネントが使用可能になり、警告が解決されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-151">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="603ae-152">ルーティング</span><span class="sxs-lookup"><span data-stu-id="603ae-152">Routing</span></span>

<span data-ttu-id="603ae-153">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-153">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="603ae-154">`@page` ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="603ae-154">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="603ae-155">実行時に、ルーターは `RouteAttribute` を持つコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="603ae-155">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="603ae-156">詳細については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="603ae-156">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="603ae-157">パラメーター</span><span class="sxs-lookup"><span data-stu-id="603ae-157">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="603ae-158">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="603ae-158">Route parameters</span></span>

<span data-ttu-id="603ae-159">コンポーネントは、`@page` ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="603ae-159">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="603ae-160">ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="603ae-160">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="603ae-161">*Pages/RouteParameter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-161">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="603ae-162">省略可能なパラメーターはサポートされていないため、前の例では2つの `@page` ディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-162">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="603ae-163">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="603ae-163">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="603ae-164">2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="603ae-164">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="603ae-165">複数のフォルダー境界をまたいでパスをキャプチャする*キャッチオール*パラメーター構文 (`*`/`**`) は、razor コンポーネント (*razor*) ではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="603ae-165">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="603ae-166">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="603ae-166">Component parameters</span></span>

<span data-ttu-id="603ae-167">コンポーネントには、コンポーネントクラスのパブリックプロパティを使用して定義されるコンポーネント*パラメーター*を含めることができます。これは、`[Parameter]` 属性を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="603ae-167">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="603ae-168">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="603ae-168">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="603ae-169">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-169">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="603ae-170">サンプルアプリの次の例では、`ParentComponent` によって `ChildComponent`の `Title` プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-170">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="603ae-171">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-171">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="603ae-172">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="603ae-172">Child content</span></span>

<span data-ttu-id="603ae-173">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-173">Components can set the content of another component.</span></span> <span data-ttu-id="603ae-174">割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。</span><span class="sxs-lookup"><span data-stu-id="603ae-174">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="603ae-175">次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す `RenderFragment`を表す `ChildContent` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="603ae-175">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="603ae-176">`ChildContent` の値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップに配置されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-176">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="603ae-177">`ChildContent` の値は、親コンポーネントから受信され、ブートストラップパネルの `panel-body`内に表示されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-177">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="603ae-178">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-178">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="603ae-179">`RenderFragment` コンテンツを受け取るプロパティには、規約によって `ChildContent` 名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-179">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="603ae-180">サンプルアプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` を表示するためのコンテンツを提供できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-180">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="603ae-181">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-181">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="603ae-182">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="603ae-182">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="603ae-183">コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-183">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="603ae-184">Splatted Razor ディレクティブ[`@attributes`](xref:mvc/views/razor#attributes)を使用してコンポーネントがレンダリングされるときに、ディクショナリ内で追加の属性をキャプチャし、要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="603ae-184">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="603ae-185">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="603ae-185">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="603ae-186">たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-186">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="603ae-187">次の例では、最初の `<input>` 要素 (`id="useIndividualParams"`) は個々のコンポーネントパラメーターを使用し、2番目の `<input>` 要素 (`id="useAttributesDict"`) は属性スプラッティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-187">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="603ae-188">パラメーターの型は、文字列キーを使用して `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-188">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="603ae-189">このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="603ae-189">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="603ae-190">両方の方法を使用して表示される `<input>` 要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="603ae-190">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="603ae-191">任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true`に設定して、`[Parameter]` 属性を使用してコンポーネントパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="603ae-191">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="603ae-192">`[Parameter]` の `CaptureUnmatchedValues` プロパティを使用すると、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="603ae-192">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="603ae-193">コンポーネントは、`CaptureUnmatchedValues`で1つのパラメーターのみを定義できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-193">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="603ae-194">`CaptureUnmatchedValues` で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-194">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="603ae-195">このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` もオプションです。</span><span class="sxs-lookup"><span data-stu-id="603ae-195">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="603ae-196">要素属性の位置を基準とした `@attributes` の位置が重要です。</span><span class="sxs-lookup"><span data-stu-id="603ae-196">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="603ae-197">要素に対して `@attributes` が splatted されると、属性は右から左 (最後から順) に処理されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-197">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="603ae-198">`Child` コンポーネントを使用するコンポーネントの次の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="603ae-198">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="603ae-199">*Parentcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-199">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="603ae-200">*Childcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-200">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="603ae-201">`Child` コンポーネントの `extra` 属性が `@attributes`の右側に設定されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-201">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="603ae-202">属性が右から左に処理されるため、`Parent` コンポーネントのレンダリングされる `<div>` には `extra="5"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="603ae-202">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="603ae-203">次の例では、`Child` コンポーネントの `<div>`で `extra` と `@attributes` の順序が逆になっています。</span><span class="sxs-lookup"><span data-stu-id="603ae-203">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="603ae-204">*Parentcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-204">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="603ae-205">*Childcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-205">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="603ae-206">`Parent` コンポーネントに表示される `<div>` には、追加の属性を通じて渡された `extra="10"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="603ae-206">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="603ae-207">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="603ae-207">Capture references to components</span></span>

<span data-ttu-id="603ae-208">コンポーネント参照を使用すると、`Show` や `Reset`などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。</span><span class="sxs-lookup"><span data-stu-id="603ae-208">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="603ae-209">コンポーネント参照をキャプチャするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="603ae-209">To capture a component reference:</span></span>

* <span data-ttu-id="603ae-210">子コンポーネントに[`@ref`](xref:mvc/views/razor#ref)属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="603ae-210">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="603ae-211">子コンポーネントと同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="603ae-211">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="603ae-212">コンポーネントがレンダリングされると、[`_loginDialog`] フィールドに `MyLoginDialog` 子コンポーネントインスタンスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-212">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="603ae-213">その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="603ae-213">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="603ae-214">`_loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="603ae-214">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="603ae-215">この時点までは、参照するものはありません。</span><span class="sxs-lookup"><span data-stu-id="603ae-215">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="603ae-216">コンポーネント参照のレンダリングが完了した後にコンポーネント参照を操作するに[は、OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](xref:blazor/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-216">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="603ae-217">コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="603ae-217">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="603ae-218">コンポーネント参照は、.NET コードでのみ使用される&mdash;JavaScript コードに渡されません。</span><span class="sxs-lookup"><span data-stu-id="603ae-218">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="603ae-219">子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="603ae-219">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="603ae-220">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="603ae-220">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="603ae-221">通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="603ae-221">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="603ae-222">状態を更新するためにコンポーネントメソッドを外部に呼び出す</span><span class="sxs-lookup"><span data-stu-id="603ae-222">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="603ae-223"> は、`SynchronizationContext` を使用して、1つの論理スレッドの実行を強制します。</span><span class="sxs-lookup"><span data-stu-id="603ae-223"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="603ae-224">Blazor によって発生するコンポーネントの[ライフサイクルメソッド](xref:blazor/lifecycle)とイベントコールバックは、この `SynchronizationContext`で実行されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-224">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="603ae-225">タイマーやその他の通知など、外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazorの `SynchronizationContext`にディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="603ae-225">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="603ae-226">たとえば、更新された状態のすべてのリッスンコンポーネントに通知できる通知*サービス*を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="603ae-226">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="603ae-227">`NotifierService` を次のように登録します。</span><span class="sxs-lookup"><span data-stu-id="603ae-227">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="603ae-228">Blazor WebAssembly、`Program.Main`にサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="603ae-228">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="603ae-229">Blazor サーバーで、`Startup.ConfigureServices`にサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="603ae-229">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="603ae-230">コンポーネントを更新するには、`NotifierService` を使用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-230">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="603ae-231">前の例では、`NotifierService` は Blazorの `SynchronizationContext`の外部でコンポーネントの `OnNotify` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="603ae-231">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="603ae-232">`InvokeAsync` は、正しいコンテキストに切り替え、レンダリングをキューに移動するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-232">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="603ae-233">\@キーを使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="603ae-233">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="603ae-234">要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazorの比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-234">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="603ae-235">通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-235">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="603ae-236">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="603ae-236">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="603ae-237">`People` コレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-237">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="603ae-238">コンポーネントのれ時に、`<DetailsEditor>` コンポーネントが異なる `Details` パラメーター値を受け取るように変更されることがあります。</span><span class="sxs-lookup"><span data-stu-id="603ae-238">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="603ae-239">これにより、予想よりも複雑な再リリースが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-239">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="603ae-240">場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-240">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="603ae-241">マッピングプロセスは、`@key` ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-241">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="603ae-242">`@key` によって、キーの値に基づいて要素またはコンポーネントの保持が比較アルゴリズムによって保証されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-242">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="603ae-243">`People` コレクションが変更されると、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンスの間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-243">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="603ae-244">`Person` が `People` の一覧から削除された場合は、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-244">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="603ae-245">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="603ae-245">Other instances are left unchanged.</span></span>
* <span data-ttu-id="603ae-246">リスト内のある位置に `Person` が挿入されると、その対応する位置に1つの新しい `<DetailsEditor>` インスタンスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-246">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="603ae-247">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="603ae-247">Other instances are left unchanged.</span></span>
* <span data-ttu-id="603ae-248">`Person` エントリが再順序付けされている場合は、対応する `<DetailsEditor>` インスタンスが UI に保持され、並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="603ae-248">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="603ae-249">場合によっては、`@key` を使用すると、回避の複雑さが最小限に抑えられるため、フォーカス位置など、DOM のステートフルな部分に対する潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-249">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="603ae-250">キーは、各コンテナー要素またはコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="603ae-250">Keys are local to each container element or component.</span></span> <span data-ttu-id="603ae-251">キーがドキュメント全体でグローバルに比較されることはありません。</span><span class="sxs-lookup"><span data-stu-id="603ae-251">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="603ae-252">\@キーを使用する場合</span><span class="sxs-lookup"><span data-stu-id="603ae-252">When to use \@key</span></span>

<span data-ttu-id="603ae-253">通常は、リストがレンダリングされるたびに (たとえば、`@foreach` ブロックで) `@key` を使用し、`@key`を定義するための適切な値が存在することを意味します。</span><span class="sxs-lookup"><span data-stu-id="603ae-253">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="603ae-254">また、`@key` を使用すると、オブジェクトが変更されたときに Blazor によって要素またはコンポーネントのサブツリーが保持されないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="603ae-254">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="603ae-255">`@currentPerson` が変更された場合、`@key` attribute ディレクティブは、`<div>` とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築する Blazor を強制的に実行します。</span><span class="sxs-lookup"><span data-stu-id="603ae-255">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="603ae-256">これは、`@currentPerson` 変更時に UI の状態が保持されないことを保証する必要がある場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="603ae-256">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="603ae-257">\@キーを使用しない場合</span><span class="sxs-lookup"><span data-stu-id="603ae-257">When not to use \@key</span></span>

<span data-ttu-id="603ae-258">`@key`を使用すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="603ae-258">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="603ae-259">パフォーマンスコストは大きくありませんが、要素またはコンポーネントの保存規則を制御することによってアプリにメリットがある場合にのみ `@key` を指定します。</span><span class="sxs-lookup"><span data-stu-id="603ae-259">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="603ae-260">`@key` が使用されていない場合でも Blazor、子要素とコンポーネントインスタンスは可能な限り保持されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-260">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="603ae-261">`@key` を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。</span><span class="sxs-lookup"><span data-stu-id="603ae-261">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="603ae-262">\@キーに使用する値</span><span class="sxs-lookup"><span data-stu-id="603ae-262">What values to use for \@key</span></span>

<span data-ttu-id="603ae-263">一般に、`@key`には、次のいずれかの値を指定するのが理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="603ae-263">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="603ae-264">モデルオブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。</span><span class="sxs-lookup"><span data-stu-id="603ae-264">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="603ae-265">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-265">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="603ae-266">一意の識別子 (たとえば、`int`型、`string`型、`Guid`型の主キー値)。</span><span class="sxs-lookup"><span data-stu-id="603ae-266">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="603ae-267">`@key` に使用される値が競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="603ae-267">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="603ae-268">競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="603ae-268">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="603ae-269">個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="603ae-269">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="603ae-270">部分クラスのサポート</span><span class="sxs-lookup"><span data-stu-id="603ae-270">Partial class support</span></span>

<span data-ttu-id="603ae-271">Razor コンポーネントは部分クラスとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-271">Razor components are generated as partial classes.</span></span> <span data-ttu-id="603ae-272">Razor コンポーネントは、次のいずれかの方法を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-272">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="603ae-273">C#コードは、1つのファイル内に HTML マークアップと Razor コードを含む[`@code`](xref:mvc/views/razor#code)ブロックで定義されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-273">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="603ae-274"> テンプレートでは、この方法を使用して Razor コンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="603ae-274"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="603ae-275">C#コードは、部分クラスとして定義されている分離コードファイルに配置されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-275">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="603ae-276">次の例は、Blazor テンプレートから生成されたアプリで `@code` ブロックを持つ既定の `Counter` コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="603ae-276">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="603ae-277">HTML マークアップ、Razor コード、 C#およびコードは、同じファイル内にあります。</span><span class="sxs-lookup"><span data-stu-id="603ae-277">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="603ae-278">*Counter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-278">*Counter.razor*:</span></span>

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

<span data-ttu-id="603ae-279">`Counter` コンポーネントは、部分クラスの分離コードファイルを使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="603ae-279">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="603ae-280">*Counter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="603ae-280">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="603ae-281">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="603ae-281">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="603ae-282">必要に応じて、部分クラスファイルに必要な名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="603ae-282">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="603ae-283">Razor コンポーネントで使用される一般的な名前空間は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="603ae-283">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="603ae-284">基底クラスの指定</span><span class="sxs-lookup"><span data-stu-id="603ae-284">Specify a base class</span></span>

<span data-ttu-id="603ae-285">[`@inherits`](xref:mvc/views/razor#inherits)ディレクティブは、コンポーネントの基底クラスを指定するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-285">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="603ae-286">次の例は、コンポーネントが基本クラス `BlazorRocksBase`を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="603ae-286">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="603ae-287">基底クラスは `ComponentBase`から派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-287">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="603ae-288">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="603ae-288">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="603ae-289">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="603ae-289">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="603ae-290">属性を指定する</span><span class="sxs-lookup"><span data-stu-id="603ae-290">Specify an attribute</span></span>

<span data-ttu-id="603ae-291">属性は、 [`@attribute`](xref:mvc/views/razor#attribute)ディレクティブを使用して Razor コンポーネントで指定できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-291">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="603ae-292">次の例では、`[Authorize]` 属性を component クラスに適用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-292">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="603ae-293">コンポーネントのインポート</span><span class="sxs-lookup"><span data-stu-id="603ae-293">Import components</span></span>

<span data-ttu-id="603ae-294">Razor で作成されるコンポーネントの名前空間は、(優先順位に従って) に基づきます。</span><span class="sxs-lookup"><span data-stu-id="603ae-294">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="603ae-295">Razor ファイル (*razor*) マークアップ (`@namespace BlazorSample.MyNamespace`) の[`@namespace`](xref:mvc/views/razor#namespace)指定。</span><span class="sxs-lookup"><span data-stu-id="603ae-295">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="603ae-296">プロジェクトファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。</span><span class="sxs-lookup"><span data-stu-id="603ae-296">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="603ae-297">プロジェクトファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクトルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="603ae-297">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="603ae-298">たとえば、フレームワークは *{PROJECT ROOT}/* *BlazorSample (* ) を名前空間 `BlazorSample.Pages`に解決します。</span><span class="sxs-lookup"><span data-stu-id="603ae-298">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="603ae-299">コンポーネントはC# 、名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="603ae-299">Components follow C# name binding rules.</span></span> <span data-ttu-id="603ae-300">この例の `Index` コンポーネントの場合、スコープ内のコンポーネントはすべてコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="603ae-300">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="603ae-301">同じフォルダー内の*ページ*。</span><span class="sxs-lookup"><span data-stu-id="603ae-301">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="603ae-302">別の名前空間を明示的に指定していない、プロジェクトのルート内のコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="603ae-302">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="603ae-303">別の名前空間で定義されているコンポーネントは、Razor の[`@using`](xref:mvc/views/razor#using)ディレクティブを使用してスコープ内に置かれます。</span><span class="sxs-lookup"><span data-stu-id="603ae-303">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="603ae-304">*BlazorSample/Shared/* フォルダーに別のコンポーネント (`NavMenu.razor`) が存在する場合は、次の `@using` ステートメントを使用して `Index.razor` でコンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-304">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="603ae-305">コンポーネントは、完全修飾名を使用して参照することもできます。この場合、 [`@using`](xref:mvc/views/razor#using)ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="603ae-305">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="603ae-306">`global::` の修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="603ae-306">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="603ae-307">エイリアス付き `using` ステートメント (`@using Foo = Bar`など) を含むコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="603ae-307">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="603ae-308">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="603ae-308">Partially qualified names aren't supported.</span></span> <span data-ttu-id="603ae-309">たとえば、`<Shared.NavMenu></Shared.NavMenu>` での `@using BlazorSample` の追加と `NavMenu.razor` の参照はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="603ae-309">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="603ae-310">条件付き HTML 要素の属性</span><span class="sxs-lookup"><span data-stu-id="603ae-310">Conditional HTML element attributes</span></span>

<span data-ttu-id="603ae-311">HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-311">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="603ae-312">値が `false` または `null`の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="603ae-312">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="603ae-313">値が `true`場合は、属性が最小化されて表示されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-313">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="603ae-314">次の例では、`IsCompleted` によって `checked` が要素のマークアップに表示されるかどうかが決まります。</span><span class="sxs-lookup"><span data-stu-id="603ae-314">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="603ae-315">`IsCompleted` が `true`場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-315">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="603ae-316">`IsCompleted` が `false`場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-316">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="603ae-317">詳細については、「<xref:mvc/views/razor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="603ae-317">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="603ae-318">[Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)などの一部の HTML 属性は、.net 型が `bool`の場合、正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="603ae-318">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="603ae-319">そのような場合は、`bool`ではなく `string` の型を使用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-319">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="603ae-320">未加工の HTML</span><span class="sxs-lookup"><span data-stu-id="603ae-320">Raw HTML</span></span>

<span data-ttu-id="603ae-321">通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="603ae-321">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="603ae-322">生の HTML をレンダリングするには、`MarkupString` 値で HTML コンテンツをラップします。</span><span class="sxs-lookup"><span data-stu-id="603ae-322">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="603ae-323">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-323">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="603ae-324">信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="603ae-324">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="603ae-325">次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加します。</span><span class="sxs-lookup"><span data-stu-id="603ae-325">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="603ae-326">カスケード値とパラメーター</span><span class="sxs-lookup"><span data-stu-id="603ae-326">Cascading values and parameters</span></span>

<span data-ttu-id="603ae-327">場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="603ae-327">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="603ae-328">カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="603ae-328">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="603ae-329">カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。</span><span class="sxs-lookup"><span data-stu-id="603ae-329">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="603ae-330">テーマの例</span><span class="sxs-lookup"><span data-stu-id="603ae-330">Theme example</span></span>

<span data-ttu-id="603ae-331">次のサンプルアプリの例では、`ThemeInfo` クラスが、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。</span><span class="sxs-lookup"><span data-stu-id="603ae-331">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="603ae-332">*UiThemeInfo/* :</span><span class="sxs-lookup"><span data-stu-id="603ae-332">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="603ae-333">先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-333">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="603ae-334">`CascadingValue` コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="603ae-334">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="603ae-335">たとえば、サンプルアプリでは、アプリのいずれかのレイアウトのテーマ情報 (`ThemeInfo`) を、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとして指定します。</span><span class="sxs-lookup"><span data-stu-id="603ae-335">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="603ae-336">`ButtonClass` には、レイアウトコンポーネントで `btn-success` の値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="603ae-336">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="603ae-337">すべての子孫コンポーネントは、`ThemeInfo` カスケードオブジェクトを介してこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-337">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="603ae-338">`CascadingValuesParametersLayout` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="603ae-338">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="603ae-339">カスケード値を使用するために、コンポーネントは `[CascadingParameter]` 属性を使用してカスケード型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="603ae-339">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="603ae-340">カスケード値は、型によってカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="603ae-340">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="603ae-341">サンプルアプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値がカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="603ae-341">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="603ae-342">パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-342">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="603ae-343">`CascadingValuesParametersTheme` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="603ae-343">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="603ae-344">同じサブツリー内で同じ型の複数の値を連鎖させるには、各 `CascadingValue` コンポーネントとそれに対応する `CascadingParameter`に一意の `Name` 文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="603ae-344">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="603ae-345">次の例では、2つの `CascadingValue` コンポーネントが名前で `MyCascadingType` の異なるインスタンスをカスケードしています。</span><span class="sxs-lookup"><span data-stu-id="603ae-345">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="603ae-346">子孫コンポーネントでは、カスケードされたパラメーターは、先祖コンポーネントの対応するカスケード値から名前で値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="603ae-346">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="603ae-347">TabSet の例</span><span class="sxs-lookup"><span data-stu-id="603ae-347">TabSet example</span></span>

<span data-ttu-id="603ae-348">カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。</span><span class="sxs-lookup"><span data-stu-id="603ae-348">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="603ae-349">たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="603ae-349">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="603ae-350">このサンプルアプリには、タブに実装されている `ITab` インターフェイスがあります。</span><span class="sxs-lookup"><span data-stu-id="603ae-350">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="603ae-351">`CascadingValuesParametersTabSet` コンポーネントは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="603ae-351">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="603ae-352">子 `Tab` コンポーネントは、パラメーターとして `TabSet`に明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="603ae-352">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="603ae-353">代わりに、子 `Tab` コンポーネントは、`TabSet`の子コンテンツの一部になります。</span><span class="sxs-lookup"><span data-stu-id="603ae-353">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="603ae-354">ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントは、*それ自体をカスケード値として提供*し、その後、子孫 `Tab` コンポーネントによって取得されます。</span><span class="sxs-lookup"><span data-stu-id="603ae-354">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="603ae-355">`TabSet` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="603ae-355">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="603ae-356">子孫の `Tab` コンポーネントは、含まれている `TabSet` をカスケード型パラメーターとしてキャプチャするので、`Tab` のコンポーネントは、どのタブがアクティブであるかを `TabSet` および座標に追加します。</span><span class="sxs-lookup"><span data-stu-id="603ae-356">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="603ae-357">`Tab` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="603ae-357">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="603ae-358">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="603ae-358">Razor templates</span></span>

<span data-ttu-id="603ae-359">レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="603ae-359">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="603ae-360">Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="603ae-360">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="603ae-361">次の例は、`RenderFragment` と `RenderFragment<T>` の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="603ae-361">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="603ae-362">レンダーフラグメントは、[テンプレートコンポーネント](xref:blazor/templated-components)に引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="603ae-362">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="603ae-363">前のコードの出力をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="603ae-363">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="603ae-364">スケーラブルベクターグラフィックス (SVG) イメージ</span><span class="sxs-lookup"><span data-stu-id="603ae-364">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="603ae-365">Blazor は HTML をレンダリングするため、スケーラブルベクターグラフィックス (svg) イメージ (*svg*) を含むブラウザーでサポートされているイメージは、`<img>` タグを介してサポートされます。</span><span class="sxs-lookup"><span data-stu-id="603ae-365">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="603ae-366">同様に、SVG イメージは、スタイルシートファイル ( *.css*) の css 規則でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="603ae-366">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="603ae-367">ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="603ae-367">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="603ae-368">コンポーネントファイル (*razor*) に `<svg>` タグを直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="603ae-368">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="603ae-369">たとえば、`<use>` タグは現在尊重されていないため、`@bind` をいくつかの SVG タグと共に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="603ae-369">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="603ae-370">今後のリリースでは、これらの制限に対処する予定です。</span><span class="sxs-lookup"><span data-stu-id="603ae-370">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="603ae-371">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="603ae-371">Additional resources</span></span>

* <span data-ttu-id="603ae-372"><xref:security/blazor/server> &ndash; には、リソース枯渇に対処する必要がある Blazor サーバーアプリを構築するためのガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="603ae-372"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
