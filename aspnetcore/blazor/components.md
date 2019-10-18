---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/components
ms.openlocfilehash: cd48111e8d601fc67e8a938fcdd686759a9ddeca
ms.sourcegitcommit: ce2bfb01f2cc7dd83f8a97da0689d232c71bcdc4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531120"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="c3b98-103">ASP.NET Core Razor コンポーネントを作成して使用する</span><span class="sxs-lookup"><span data-stu-id="c3b98-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="c3b98-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c3b98-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c3b98-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c3b98-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c3b98-106">Blazor アプリは*コンポーネント*を使用して構築されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="c3b98-107">コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="c3b98-108">コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="c3b98-109">コンポーネントは柔軟で軽量です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="c3b98-110">入れ子にして再利用したり、プロジェクト間で共有したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="c3b98-111">コンポーネントクラス</span><span class="sxs-lookup"><span data-stu-id="c3b98-111">Component classes</span></span>

<span data-ttu-id="c3b98-112">コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="c3b98-113">Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="c3b98-114">コンポーネントの名前は、大文字で始まる必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="c3b98-115">たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="c3b98-116">コンポーネントの UI は、HTML を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="c3b98-117">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="c3b98-118">アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="c3b98-119">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="c3b98-120">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="c3b98-121">@No__t-0 ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッド、またはその他のコンポーネントロジックを定義するために指定されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="c3b98-122">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="c3b98-123">ASP.NET Core 3.0 の以前のプレビューでは、`@functions` のブロックが Razor コンポーネントの `@code` ブロックと同じ目的で使用されていました。</span><span class="sxs-lookup"><span data-stu-id="c3b98-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="c3b98-124">`@functions` のブロックは Razor コンポーネントで引き続き機能しますが、ASP.NET Core 3.0 Preview 6 以降では `@code` ブロックを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="c3b98-125">コンポーネントメンバーは、`@` で始まる式を使用してC# 、コンポーネントのレンダリングロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="c3b98-126">たとえば、 C#フィールドは、フィールド名にプレフィックス `@` を付けることによって表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="c3b98-127">次の例では、が評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="c3b98-128">`font-style` の CSS プロパティ値に `_headingFontStyle` します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="c3b98-129">`<h1>` 要素の内容に `_headingText` します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="c3b98-130">コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="c3b98-131">Blazor は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="c3b98-132">コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="c3b98-133">Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="c3b98-134">ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="c3b98-135">カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を、親コンポーネントまたはアプリのインポートのいずれかの*razor*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="c3b98-136">たとえば、次の名前空間は、アプリのルート名前空間が `WebApplication` の場合*に、コンポーネントフォルダー内*のコンポーネントを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="c3b98-137">コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="c3b98-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="c3b98-138">既存の Razor Pages および MVC アプリでコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="c3b98-139">Razor コンポーネントを使用するために既存のページやビューを書き直す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="c3b98-140">ページまたはビューが表示されると、コンポーネントは同時に prerendered されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="c3b98-141">ページまたはビューからコンポーネントを表示するには、`RenderComponentAsync<TComponent>` の HTML ヘルパーメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="c3b98-142">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c3b98-143">コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="c3b98-144">コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="c3b98-145">コンポーネントがどのようにレンダリングされ、コンポーネントの状態が Blazor Server apps で管理されるかの詳細については、@no__t の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-145">For more information on how components are rendered and component state is managed in Blazor Server apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="c3b98-146">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="c3b98-146">Use components</span></span>

<span data-ttu-id="c3b98-147">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="c3b98-148">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="c3b98-149">属性のバインドでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-149">Attribute binding is case sensitive.</span></span> <span data-ttu-id="c3b98-150">たとえば、`@bind` は有効で、`@Bind` は無効です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-150">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="c3b98-151">*Index. razor*の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-151">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="c3b98-152">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-152">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="c3b98-153">コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-153">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="c3b98-154">コンポーネントの名前空間に `@using` ステートメントを追加すると、コンポーネントが使用可能になり、警告が削除されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-154">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="c3b98-155">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="c3b98-155">Component parameters</span></span>

<span data-ttu-id="c3b98-156">コンポーネントには、コンポーネントクラスのパブリックプロパティを使用して定義されるコンポーネント*パラメーター*を含めることができます。これは、`[Parameter]` 属性を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-156">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="c3b98-157">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="c3b98-157">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="c3b98-158">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-158">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="c3b98-159">次の例では、`ParentComponent` は `ChildComponent` の `Title` プロパティの値を設定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-159">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="c3b98-160">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-160">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="c3b98-161">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="c3b98-161">Child content</span></span>

<span data-ttu-id="c3b98-162">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-162">Components can set the content of another component.</span></span> <span data-ttu-id="c3b98-163">割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-163">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="c3b98-164">次の例では、`ChildComponent` は、レンダリングする UI のセグメントを表す、`RenderFragment` を表す `ChildContent` プロパティを持っています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-164">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="c3b98-165">@No__t-0 の値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップ内に配置されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-165">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="c3b98-166">@No__t-0 の値は、親コンポーネントから受信され、ブートストラップパネルの `panel-body` 内に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-166">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="c3b98-167">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-167">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="c3b98-168">@No__t 0 のコンテンツを受け取るプロパティには、規則に従って `ChildContent` を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-168">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="c3b98-169">次の `ParentComponent` は、`<ChildComponent>` タグ内にコンテンツを配置することによって、@no__t を表示するためのコンテンツを提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-169">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="c3b98-170">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-170">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="c3b98-171">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="c3b98-171">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="c3b98-172">コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-172">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="c3b98-173">追加の属性は、ディクショナリ内でキャプチャし、 [@no__t 2](xref:mvc/views/razor#attributes) Razor ディレクティブを使用してコンポーネントがレンダリングされるときに要素に*splatted*することができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-173">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="c3b98-174">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-174">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="c3b98-175">たとえば、多数のパラメーターをサポートする @no__t 0 に対して個別に属性を定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-175">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="c3b98-176">次の例では、最初の `<input>` 要素 (`id="useIndividualParams"`) が個々のコンポーネントパラメーターを使用し、2番目の `<input>` 要素 (`id="useAttributesDict"`) は属性スプラッティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-176">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
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

<span data-ttu-id="c3b98-177">パラメーターの型は、文字列キーを使用して `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-177">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="c3b98-178">このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-178">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="c3b98-179">両方の方法を使用して表示される @no__t 0 の要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-179">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="c3b98-180">任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true` に設定して、`[Parameter]` 属性を使用してコンポーネントパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-180">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="c3b98-181">@No__t_1 の `CaptureUnmatchedValues` プロパティを使用すると、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-181">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="c3b98-182">コンポーネントで定義できるのは、`CaptureUnmatchedValues` の1つのパラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-182">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="c3b98-183">@No__t-0 と共に使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-183">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="c3b98-184">`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` は、このシナリオのオプションでもあります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-184">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="c3b98-185">データ バインディング</span><span class="sxs-lookup"><span data-stu-id="c3b98-185">Data binding</span></span>

<span data-ttu-id="c3b98-186">コンポーネントと DOM 要素の両方に対するデータバインディングは、 [@bind](xref:mvc/views/razor#bind)属性を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-186">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="c3b98-187">次の例では、`CurrentValue` プロパティをテキストボックスの値にバインドします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-187">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```cshtml
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c3b98-188">テキストボックスがフォーカスを失うと、プロパティの値が更新されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-188">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="c3b98-189">テキストボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-189">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="c3b98-190">イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、*通常*、イベントハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-190">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="c3b98-191">@No__t-0 を `CurrentValue` プロパティ (`<input @bind="CurrentValue" />`) と共に使用することは、基本的に次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-191">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c3b98-192">コンポーネントがレンダリングされると、入力要素の @no__t 0 は、`CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-192">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="c3b98-193">ユーザーがテキストボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-193">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="c3b98-194">実際には、`@bind` は型変換が実行されるケースを処理するため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-194">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="c3b98-195">原則として、`@bind` は、式の現在の値を `value` 属性に関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-195">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="c3b98-196">@No__t-1 構文を使用した @no__t 0 イベントの処理に加えて、プロパティまたはフィールドを他のイベントを使用してバインドするには、 [@bind-value](xref:mvc/views/razor#bind)属性に `event` パラメーター ([@bind-value:event](xref:mvc/views/razor#bind)) を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-196">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="c3b98-197">次の例では、`oninput` イベントの `CurrentValue` プロパティをバインドします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-197">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c3b98-198">要素がフォーカスを失ったときに発生する `onchange` とは異なり、テキストボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-198">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="c3b98-199">**解析不可能値**</span><span class="sxs-lookup"><span data-stu-id="c3b98-199">**Unparsable values**</span></span>

<span data-ttu-id="c3b98-200">データバインド要素に解析できない値を指定すると、バインドイベントがトリガーされたときに、解析されていない値が自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-200">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="c3b98-201">次のシナリオについて検討してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-201">Consider the following scenario:</span></span>

* <span data-ttu-id="c3b98-202">@No__t-0 要素は、初期値 `123` を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-202">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="c3b98-203">ユーザーは、要素の値をページの `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-203">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="c3b98-204">前のシナリオでは、要素の値は `123` に戻されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-204">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="c3b98-205">@No__t-1 の元の値を優先して値 `123.45` が拒否されると、ユーザーはその値が受け入れられていないことを認識します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-205">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="c3b98-206">既定では、バインドは要素の @no__t 0 イベント (`@bind="{PROPERTY OR FIELD}"`) に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-206">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="c3b98-207">別のイベントを設定するには、`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-207">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="c3b98-208">@No__t-0 イベント (`@bind-value:event="oninput"`) の場合、解析されていない値を導入するキーストロークの後に再設定が発生します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-208">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="c3b98-209">@No__t の-1 バインド型で `oninput` イベントを対象とする場合、ユーザーは `.` 文字を入力できません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-209">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="c3b98-210">@No__t 0 文字がすぐに削除されます。そのため、ユーザーは、整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-210">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="c3b98-211">@No__t-0 イベントの値を元に戻すことは、ユーザーが解析できない `<input>` 値のクリアを許可する必要がある場合など、理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-211">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="c3b98-212">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-212">Alternatives include:</span></span>

* <span data-ttu-id="c3b98-213">@No__t-0 イベントは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-213">Don't use the `oninput` event.</span></span> <span data-ttu-id="c3b98-214">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-214">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="c3b98-215">@No__t-0 や `string` などの null 許容型にバインドし、無効なエントリを処理するカスタムロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-215">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="c3b98-216">@No__t-1 や `InputDate` などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-216">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="c3b98-217">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-217">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="c3b98-218">フォーム検証コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c3b98-218">Form validation components:</span></span>
  * <span data-ttu-id="c3b98-219">ユーザーが無効な入力を提供し、関連付けられている `EditContext` に検証エラーを受信することを許可します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-219">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="c3b98-220">追加の web フォームデータを入力するユーザーに干渉することなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-220">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="c3b98-221">**グローバリゼーション**</span><span class="sxs-lookup"><span data-stu-id="c3b98-221">**Globalization**</span></span>

<span data-ttu-id="c3b98-222">`@bind` の値は、現在のカルチャの規則を使用して表示および解析するように書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-222">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="c3b98-223">現在のカルチャには、@no__t 0 のプロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-223">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="c3b98-224">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-224">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="c3b98-225">上記のフィールド型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-225">The preceding field types:</span></span>

* <span data-ttu-id="c3b98-226">は、適切なブラウザーベースの書式規則を使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-226">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="c3b98-227">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-227">Can't contain free-form text.</span></span>
* <span data-ttu-id="c3b98-228">ブラウザーの実装に基づいてユーザーの操作特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-228">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="c3b98-229">次のフィールド型には特定の書式要件があり、Blazor では現在サポートされていません。これは、すべての主要なブラウザーでサポートされていないためです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-229">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="c3b98-230">`@bind` は、値の解析と書式設定に <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供するために、`@bind:culture` パラメーターをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-230">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="c3b98-231">@No__t-0 および `number` フィールド型を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-231">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="c3b98-232">`date` および `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-232">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="c3b98-233">ユーザーのカルチャを設定する方法については、「[ローカリゼーション](#localization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-233">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="c3b98-234">**書式指定文字列**</span><span class="sxs-lookup"><span data-stu-id="c3b98-234">**Format strings**</span></span>

<span data-ttu-id="c3b98-235">データバインディングは[、@bind:format](xref:mvc/views/razor#bind)を使用して @no__t 0 の書式指定文字列で動作します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-235">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="c3b98-236">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-236">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c3b98-237">前のコードでは、@no__t 0 要素のフィールドの種類 (`type`) は既定で `text` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-237">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="c3b98-238">`@bind:format` は、次の .NET 型のバインドに対してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-238">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="c3b98-239"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c3b98-239"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="c3b98-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c3b98-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="c3b98-241">@No__t-0 属性は、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-241">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c3b98-242">この形式は、@no__t 0 イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-242">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c3b98-243">Blazor には日付を書式設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-243">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="c3b98-244">**コンポーネントのパラメーター**</span><span class="sxs-lookup"><span data-stu-id="c3b98-244">**Component parameters**</span></span>

<span data-ttu-id="c3b98-245">バインディングはコンポーネントパラメーターを認識します。 `@bind-{property}` はコンポーネント間でプロパティ値をバインドできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-245">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="c3b98-246">次の子コンポーネント (`ChildComponent`) には、`Year` のコンポーネントパラメーターと `YearChanged` のコールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-246">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="c3b98-247">`EventCallback<T>` については、「 [Eventcallback](#eventcallback) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-247">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="c3b98-248">次の親コンポーネントは `ChildComponent` を使用し、親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-248">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="c3b98-249">@No__t を読み込むと、次のマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-249">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="c3b98-250">@No__t-0 プロパティの値が、`ParentComponent` の [] ボタンを選択して変更された場合は、`ChildComponent` の `Year` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-250">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="c3b98-251">@No__t-1 が再度実行されると、`Year` の新しい値が UI に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-251">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="c3b98-252">@No__t-0 パラメーターは、`Year` パラメーターの型に一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-252">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="c3b98-253">慣例により、@no__t 0 は基本的に書き込みと同じになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-253">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="c3b98-254">一般に、プロパティは、`@bind-property:event` 属性を使用して、対応するイベントハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-254">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="c3b98-255">たとえば、プロパティ `MyProp` は、次の2つの属性を使用して `MyEventHandler` にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-255">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="c3b98-256">イベント処理</span><span class="sxs-lookup"><span data-stu-id="c3b98-256">Event handling</span></span>

<span data-ttu-id="c3b98-257">Razor コンポーネントは、イベント処理機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-257">Razor components provide event handling features.</span></span> <span data-ttu-id="c3b98-258">@No__t-0 という名前の HTML 要素属性 (たとえば、`onclick`、`onsubmit`) にデリゲート型の値がある場合、Razor コンポーネントは属性の値をイベントハンドラーとして扱います。</span><span class="sxs-lookup"><span data-stu-id="c3b98-258">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="c3b98-259">属性の名前は常に[-1 {event} @no__t](xref:mvc/views/razor#onevent)書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-259">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="c3b98-260">次のコードは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-260">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="c3b98-261">次のコードは、UI でチェックボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-261">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c3b98-262">イベントハンドラーを非同期にして、@no__t 0 を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-262">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c3b98-263">@No__t-0 を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-263">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="c3b98-264">例外は、発生するとログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-264">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c3b98-265">次の例では、ボタンが選択されたときに `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-265">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="c3b98-266">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="c3b98-266">Event argument types</span></span>

<span data-ttu-id="c3b98-267">イベントによっては、イベント引数の型が許可されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-267">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c3b98-268">これらのイベントの種類のいずれかにアクセスする必要がない場合は、メソッドの呼び出しで必要とされません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-268">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="c3b98-269">サポートされている `EventArgs` を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-269">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="c3b98-270">event</span><span class="sxs-lookup"><span data-stu-id="c3b98-270">Event</span></span> | <span data-ttu-id="c3b98-271">インスタンス</span><span class="sxs-lookup"><span data-stu-id="c3b98-271">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="c3b98-272">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="c3b98-272">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="c3b98-273">抗力</span><span class="sxs-lookup"><span data-stu-id="c3b98-273">Drag</span></span>             | <span data-ttu-id="c3b98-274">`DragEventArgs` &ndash; `DataTransfer` および `DataTransferItem` は、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-274">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="c3b98-275">Error</span><span class="sxs-lookup"><span data-stu-id="c3b98-275">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="c3b98-276">フォーカス</span><span class="sxs-lookup"><span data-stu-id="c3b98-276">Focus</span></span>            | <span data-ttu-id="c3b98-277">`FocusEventArgs` &ndash; に `relatedTarget` のサポートは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-277">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c3b98-278">`<input>` の変更</span><span class="sxs-lookup"><span data-stu-id="c3b98-278">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="c3b98-279">キーボード</span><span class="sxs-lookup"><span data-stu-id="c3b98-279">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="c3b98-280">マウス</span><span class="sxs-lookup"><span data-stu-id="c3b98-280">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="c3b98-281">マウスポインター</span><span class="sxs-lookup"><span data-stu-id="c3b98-281">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="c3b98-282">マウスホイール</span><span class="sxs-lookup"><span data-stu-id="c3b98-282">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="c3b98-283">進行状況</span><span class="sxs-lookup"><span data-stu-id="c3b98-283">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="c3b98-284">タッチ</span><span class="sxs-lookup"><span data-stu-id="c3b98-284">Touch</span></span>            | <span data-ttu-id="c3b98-285">`TouchEventArgs` &ndash; `TouchPoint` は、タッチ感度デバイス上の1つの連絡先ポイントを表します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-285">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="c3b98-286">前の表に示したイベントのプロパティとイベント処理動作の詳細については、「[参照ソースの EventArgs クラス (aspnet/AspNetCore release/3.0 分岐)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-286">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="c3b98-287">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="c3b98-287">Lambda expressions</span></span>

<span data-ttu-id="c3b98-288">ラムダ式を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-288">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c3b98-289">多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-289">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c3b98-290">次の例では、3つのボタンを作成します。各ボタンは、UI で選択されたときに、イベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-290">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="c3b98-291">ラムダ式では、@no__t ループのループ変数 (`i`) を**直接使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="c3b98-291">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="c3b98-292">それ以外の場合、すべてのラムダ式で同じ変数が使用され、すべてのラムダで @no__t 0 の値が同じになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-292">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="c3b98-293">常にローカル変数 (前の例では `buttonNumber`) で値をキャプチャし、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-293">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="c3b98-294">EventCallback</span><span class="sxs-lookup"><span data-stu-id="c3b98-294">EventCallback</span></span>

<span data-ttu-id="c3b98-295">入れ子になったコンポーネントを使用する一般的なシナリオは、子コンポーネントのイベントが発生したときに親コンポーネントのメソッドを実行することです。たとえば、子に `onclick` イベントが発生した場合などです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-295">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="c3b98-296">コンポーネント間でイベントを公開するには、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-296">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="c3b98-297">親コンポーネントは、コールバックメソッドを子コンポーネントの @no__t 0 に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-297">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="c3b98-298">サンプルアプリの `ChildComponent` は、ボタンの @no__t ハンドラーが、サンプルの `ParentComponent` から @no__t 2 デリゲートを受け取るように設定されている方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-298">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c3b98-299">@No__t-0 は、`MouseEventArgs` で入力されます。これは、周辺機器の @no__t 2 イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-299">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c3b98-300">@No__t-0 は、子の `EventCallback<T>` を `ShowMessage` メソッドに設定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-300">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="c3b98-301">@No__t-0 でボタンが選択されると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-301">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c3b98-302">@No__t-0 の @no__t メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-302">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c3b98-303">`messageText` が更新され、`ParentComponent` に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-303">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c3b98-304">コールバックのメソッド (`ShowMessage`) で `StateHasChanged` を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-304">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c3b98-305">`StateHasChanged` は、子イベントと同様に、子の内部で実行されるイベントハンドラーでコンポーネントの実行がトリガーされるのと同様に、`ParentComponent` をレンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-305">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c3b98-306">`EventCallback` および `EventCallback<T>` は、非同期デリゲートを許可します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-306">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="c3b98-307">`EventCallback<T>` は厳密に型指定され、特定の引数の型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-307">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="c3b98-308">`EventCallback` は弱く型指定され、任意の引数の型を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-308">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="c3b98-309">@No__t-2 を使用して @no__t 0 または `EventCallback<T>` を呼び出し、<xref:System.Threading.Tasks.Task> を待機します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-309">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="c3b98-310">イベント処理とバインドコンポーネントのパラメーターには `EventCallback` と `EventCallback<T>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-310">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="c3b98-311">厳密に型指定された `EventCallback<T>` `EventCallback` を優先します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-311">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="c3b98-312">`EventCallback<T>` を使用すると、コンポーネントのユーザーに対してより適切なエラーフィードバックが得られます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-312">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="c3b98-313">他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-313">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c3b98-314">コールバックに値が渡されない場合は、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-314">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="chained-bind"></a><span data-ttu-id="c3b98-315">チェーンバインド</span><span class="sxs-lookup"><span data-stu-id="c3b98-315">Chained bind</span></span>

<span data-ttu-id="c3b98-316">一般的なシナリオでは、データバインドパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-316">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="c3b98-317">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーンバインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-317">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="c3b98-318">チェーンバインドは、ページの要素に @no__t 0 構文で実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-318">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="c3b98-319">イベントハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-319">The event handler and value must be specified separately.</span></span> <span data-ttu-id="c3b98-320">ただし、親コンポーネントでは、`@bind` 構文をコンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-320">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="c3b98-321">次の `PasswordField` コンポーネント (*Passwordfield*):</span><span class="sxs-lookup"><span data-stu-id="c3b98-321">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="c3b98-322">@No__t 0 の要素の値を `Password` プロパティに設定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-322">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="c3b98-323">[Eventcallback](#eventcallback)を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-323">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="c3b98-324">@No__t-0 コンポーネントが別のコンポーネントで使用されています:</span><span class="sxs-lookup"><span data-stu-id="c3b98-324">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="c3b98-325">前の例で、パスワードの確認またはトラップエラーを実行するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-325">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="c3b98-326">@No__t-0 のバッキングフィールドを作成します (次のコード例では、`password`)。</span><span class="sxs-lookup"><span data-stu-id="c3b98-326">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="c3b98-327">@No__t-0 setter でチェックまたはトラップエラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-327">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="c3b98-328">次の例では、パスワードの値にスペースが使用されている場合に、ユーザーにすぐにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-328">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="c3b98-329">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="c3b98-329">Capture references to components</span></span>

<span data-ttu-id="c3b98-330">コンポーネント参照を使用すると、`Show` や `Reset` などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-330">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="c3b98-331">コンポーネント参照をキャプチャするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-331">To capture a component reference:</span></span>

* <span data-ttu-id="c3b98-332">子コンポーネントに[@ref](xref:mvc/views/razor#ref)属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-332">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="c3b98-333">子コンポーネントと同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-333">Define a field with the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="c3b98-334">コンポーネントがレンダリングされると、@no__t 0 のフィールドに @no__t 1 つの子コンポーネントインスタンスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-334">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="c3b98-335">その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-335">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3b98-336">@No__t-0 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-336">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="c3b98-337">この時点までは、参照するものはありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-337">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="c3b98-338">コンポーネント参照のレンダリングが完了した後にコンポーネント参照を操作するに[は、OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](#lifecycle-methods)を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-338">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](#lifecycle-methods).</span></span>

<span data-ttu-id="c3b98-339">コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-339">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="c3b98-340">コンポーネント参照は、.NET コードでのみ使用される JavaScript コード @ no__t に渡されません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-340">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="c3b98-341">子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-341">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="c3b98-342">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-342">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="c3b98-343">通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-343">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="c3b98-344">状態を更新するためにコンポーネントメソッドを外部に呼び出す</span><span class="sxs-lookup"><span data-stu-id="c3b98-344">Invoke component methods externally to update state</span></span>

<span data-ttu-id="c3b98-345">Blazor は、1つの論理スレッドの実行を強制するために `SynchronizationContext` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-345">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="c3b98-346">Blazor によって発生するコンポーネントのライフサイクルメソッドとイベントコールバックは、この `SynchronizationContext` で実行されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-346">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="c3b98-347">タイマーやその他の通知など、外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。このメソッドは、Blazor の `SynchronizationContext` にディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-347">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="c3b98-348">たとえば、更新された状態のすべてのリッスンコンポーネントに通知できる通知*サービス*を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-348">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="c3b98-349">コンポーネントを更新するには `NotifierService` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-349">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="c3b98-350">前の例では、`NotifierService` は、Blazor の `SynchronizationContext` の外側でコンポーネントの @no__t メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-350">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="c3b98-351">`InvokeAsync` は、正しいコンテキストに切り替え、レンダリングをキューに移動するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-351">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="c3b98-352">@No__t-0key を使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="c3b98-352">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="c3b98-353">要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-353">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="c3b98-354">通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-354">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="c3b98-355">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-355">Consider the following example:</span></span>

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

<span data-ttu-id="c3b98-356">@No__t 0 のコレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-356">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="c3b98-357">コンポーネントのれ時に、@no__t 0 のコンポーネントが異なる `Details` パラメーター値を受け取るように変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-357">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="c3b98-358">これにより、予想よりも複雑な再リリースが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-358">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="c3b98-359">場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-359">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="c3b98-360">マッピングプロセスは、`@key` ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-360">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="c3b98-361">`@key` にすると、キーの値に基づいて要素またはコンポーネントの保持が比較アルゴリズムによって保証されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-361">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="c3b98-362">@No__t 0 のコレクションが変更されると、比較アルゴリズムによって、@no__t インスタンスと @no__t 2 インスタンスの間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-362">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="c3b98-363">@No__t-0 が `People` の一覧から削除された場合は、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-363">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="c3b98-364">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-364">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c3b98-365">リスト内のある位置に @no__t 0 が挿入されると、その対応する位置に1つの新しい @no__t インスタンスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-365">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="c3b98-366">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-366">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c3b98-367">@No__t-0 のエントリが並べ替えられた場合、対応する `<DetailsEditor>` インスタンスは、UI に保持されて順序が変更されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-367">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="c3b98-368">シナリオによっては、`@key` を使用すると、回避の複雑さが最小限に抑えられ、フォーカス位置など、DOM のステートフルな部分に対する潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-368">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3b98-369">キーは、各コンテナー要素またはコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-369">Keys are local to each container element or component.</span></span> <span data-ttu-id="c3b98-370">キーがドキュメント全体でグローバルに比較されることはありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-370">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="c3b98-371">@No__t-0key を使用する場合</span><span class="sxs-lookup"><span data-stu-id="c3b98-371">When to use \@key</span></span>

<span data-ttu-id="c3b98-372">通常は、リストがレンダリングされるたびに (たとえば、`@foreach` ブロックで) `@key` を使用し、@no__t を定義するための適切な値が存在することを意味します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-372">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="c3b98-373">また `@key` を使用して、オブジェクトが変更されたときに Blazor が要素またはコンポーネントのサブツリーを保持しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-373">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="c3b98-374">@No__t-0 が変更された場合、`@key` attribute ディレクティブは、Blazor が強制的に `<div>` とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-374">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="c3b98-375">これは、`@currentPerson` の変更時に UI の状態が保持されないことを保証する必要がある場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-375">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="c3b98-376">@No__t-0key を使用しない場合</span><span class="sxs-lookup"><span data-stu-id="c3b98-376">When not to use \@key</span></span>

<span data-ttu-id="c3b98-377">@No__t-0 を使用して比較すると、パフォーマンスコストが発生します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-377">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="c3b98-378">パフォーマンスコストは大きくありませんが、要素またはコンポーネントの保存ルールを制御することによってアプリが恩恵を受ける場合は、`@key` を指定するだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-378">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="c3b98-379">@No__t-0 が使用されていない場合でも、Blazor は子要素とコンポーネントインスタンスを可能な限り保持します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-379">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="c3b98-380">@No__t 0 を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-380">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="c3b98-381">@No__t-0key に使用する値</span><span class="sxs-lookup"><span data-stu-id="c3b98-381">What values to use for \@key</span></span>

<span data-ttu-id="c3b98-382">一般に、`@key` には次のいずれかの値を指定するのが理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-382">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="c3b98-383">モデルオブジェクトインスタンス (たとえば、前の例のように @no__t 0 のインスタンス)。</span><span class="sxs-lookup"><span data-stu-id="c3b98-383">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="c3b98-384">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-384">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="c3b98-385">一意の識別子 (たとえば、型 `int`、`string`、または `Guid`) の主キー値。</span><span class="sxs-lookup"><span data-stu-id="c3b98-385">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="c3b98-386">@No__t-0 に使用される値が競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-386">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="c3b98-387">競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-387">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="c3b98-388">個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-388">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="c3b98-389">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="c3b98-389">Lifecycle methods</span></span>

<span data-ttu-id="c3b98-390">`OnInitializedAsync` および `OnInitialized` は、コンポーネントを初期化するコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-390">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="c3b98-391">非同期操作を実行するには、操作で `OnInitializedAsync` および `await` キーワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-391">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="c3b98-392">@No__t 0 のライフサイクルイベント中に、コンポーネントの初期化時に非同期作業を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-392">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="c3b98-393">同期操作の場合は、`OnInitialized` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-393">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="c3b98-394">`OnParametersSetAsync` および `OnParametersSet` は、コンポーネントが親からパラメーターを受け取り、値がプロパティに割り当てられるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-394">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="c3b98-395">これらのメソッドは、コンポーネントの初期化の後、およびコンポーネントがレンダリングされるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-395">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="c3b98-396">パラメーターとプロパティ値を適用するときの非同期処理は、@no__t 0 のライフサイクルイベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-396">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="c3b98-397">`OnAfterRenderAsync` および `OnAfterRender` は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-397">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="c3b98-398">この時点で、要素参照とコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-398">Element and component references are populated at this point.</span></span> <span data-ttu-id="c3b98-399">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-399">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="c3b98-400">サーバーでのプリレンダリング時に `OnAfterRender` は*呼び出されません。*</span><span class="sxs-lookup"><span data-stu-id="c3b98-400">`OnAfterRender` *isn't called when prerendering on the server.*</span></span>

<span data-ttu-id="c3b98-401">@No__t-1 と `OnAfterRender` の `firstRender` パラメーターは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-401">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="c3b98-402">コンポーネントインスタンスを初めて呼び出すときは、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-402">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="c3b98-403">初期化作業が1回だけ実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-403">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="c3b98-404">@No__t 0 のライフサイクルイベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-404">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="c3b98-405">レンダー時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="c3b98-405">Handle incomplete async actions at render</span></span>

<span data-ttu-id="c3b98-406">ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-406">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="c3b98-407">ライフサイクルメソッドの実行中に、オブジェクトが0に @no__t なっているか、データが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-407">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="c3b98-408">オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-408">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="c3b98-409">オブジェクトが 0 @no__t のときに、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-409">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="c3b98-410">Blazor テンプレートの @no__t 0 コンポーネントで `OnInitializedAsync` は、予測データの受信 (`forecasts`) に上書きされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-410">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="c3b98-411">@No__t-0 が `null` の場合、ユーザーに読み込みメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-411">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="c3b98-412">@No__t-1 によって返された @no__t 0 が完了すると、コンポーネントは更新された状態とピアリングされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-412">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="c3b98-413">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-413">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="c3b98-414">パラメーターが設定される前にコードを実行する</span><span class="sxs-lookup"><span data-stu-id="c3b98-414">Execute code before parameters are set</span></span>

<span data-ttu-id="c3b98-415">`SetParameters` は、パラメーターを設定する前にコードを実行するためにオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-415">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="c3b98-416">@No__t-0 が呼び出されていない場合、カスタムコードは、必要に応じて入力パラメーターの値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-416">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="c3b98-417">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-417">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="c3b98-418">UI の更新を抑制する</span><span class="sxs-lookup"><span data-stu-id="c3b98-418">Suppress refreshing of the UI</span></span>

<span data-ttu-id="c3b98-419">`ShouldRender` は、UI の更新を抑制するためにオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-419">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="c3b98-420">実装によって @no__t 0 が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-420">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="c3b98-421">@No__t-0 がオーバーライドされた場合でも、コンポーネントは常に最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-421">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="c3b98-422">IDisposable によるコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="c3b98-422">Component disposal with IDisposable</span></span>

<span data-ttu-id="c3b98-423">コンポーネントで <xref:System.IDisposable> が実装されている場合、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-423">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="c3b98-424">次のコンポーネントでは、`@implements IDisposable` および `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-424">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="c3b98-425">ルーティング</span><span class="sxs-lookup"><span data-stu-id="c3b98-425">Routing</span></span>

<span data-ttu-id="c3b98-426">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-426">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="c3b98-427">@No__t 0 ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスには、ルートテンプレートを指定する @no__t 1 が与えられます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-427">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="c3b98-428">実行時に、ルーターは @no__t 0 を持つコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-428">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="c3b98-429">コンポーネントには、複数のルートテンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-429">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c3b98-430">次のコンポーネントは `/BlazorRoute` および `/DifferentBlazorRoute` の要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-430">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="c3b98-431">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="c3b98-431">Route parameters</span></span>

<span data-ttu-id="c3b98-432">コンポーネントは、`@page` ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-432">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="c3b98-433">ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-433">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="c3b98-434">*ルートパラメーターコンポーネント*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-434">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="c3b98-435">省略可能なパラメーターはサポートされていないため、上記の例では2つの `@page` ディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-435">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="c3b98-436">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-436">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c3b98-437">2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、`Text` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-437">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="c3b98-438">"分離コード" エクスペリエンスの基底クラスの継承</span><span class="sxs-lookup"><span data-stu-id="c3b98-438">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="c3b98-439">コンポーネントファイルは、HTML マークC#アップと処理コードを同じファイルに混在させます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-439">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="c3b98-440">@No__t-0 ディレクティブを使用すると、Blazor アプリに、コンポーネントマークアップと処理コードを分離する "分離コード" エクスペリエンスを提供できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-440">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="c3b98-441">この[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)では、コンポーネントが基本クラス (`BlazorRocksBase`) を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-441">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="c3b98-442">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-442">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="c3b98-443">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3b98-443">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="c3b98-444">基底クラスは `ComponentBase` から派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-444">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="c3b98-445">コンポーネントのインポート</span><span class="sxs-lookup"><span data-stu-id="c3b98-445">Import components</span></span>

<span data-ttu-id="c3b98-446">Razor で作成されるコンポーネントの名前空間は、(優先順位に従って) に基づきます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-446">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="c3b98-447">Razor ファイル (*razor*) マークアップ (`@namespace BlazorSample.MyNamespace`) で[@namespace](xref:mvc/views/razor#namespace)の指定。</span><span class="sxs-lookup"><span data-stu-id="c3b98-447">[@namespace](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="c3b98-448">プロジェクトファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。</span><span class="sxs-lookup"><span data-stu-id="c3b98-448">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="c3b98-449">プロジェクトファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクトルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="c3b98-449">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="c3b98-450">たとえば、フレームワークによって *{PROJECT ROOT}/* *BlazorSample (* ) が名前 @no__t 空間に解決されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-450">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="c3b98-451">コンポーネントはC# 、名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="c3b98-451">Components follow C# name binding rules.</span></span> <span data-ttu-id="c3b98-452">この例の `Index` コンポーネントの場合、スコープ内のコンポーネントはすべてコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-452">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="c3b98-453">同じフォルダー内の*ページ*。</span><span class="sxs-lookup"><span data-stu-id="c3b98-453">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="c3b98-454">別の名前空間を明示的に指定していない、プロジェクトのルート内のコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="c3b98-454">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="c3b98-455">別の名前空間で定義されているコンポーネントは、Razor の[@using](xref:mvc/views/razor#using)ディレクティブを使用してスコープ内に入ります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-455">Components defined in a different namespace are brought into scope using Razor's [@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="c3b98-456">*BlazorSample/Shared/* フォルダーに別のコンポーネント (`NavMenu.razor`) が存在する場合は、次の `@using` ステートメントを使用して、@no__t 2 でコンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-456">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="c3b98-457">コンポーネントは、完全修飾名を使用して参照することもできます。この場合、 [@using](xref:mvc/views/razor#using)ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-457">Components can also be referenced using their fully qualified names, which doesn't require the [@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="c3b98-458">@No__t-0 の修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-458">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="c3b98-459">エイリアスを使用した `using` ステートメント (たとえば、`@using Foo = Bar`) を持つコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-459">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="c3b98-460">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-460">Partially qualified names aren't supported.</span></span> <span data-ttu-id="c3b98-461">たとえば、`@using BlazorSample` を追加し、`<Shared.NavMenu></Shared.NavMenu>` で `NavMenu.razor` を参照することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-461">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="c3b98-462">条件付き HTML 要素の属性</span><span class="sxs-lookup"><span data-stu-id="c3b98-462">Conditional HTML element attributes</span></span>

<span data-ttu-id="c3b98-463">HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-463">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="c3b98-464">値が `false` または `null` の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-464">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="c3b98-465">値が @no__t 0 の場合は、属性が最小化されて表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-465">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="c3b98-466">次の例では、`IsCompleted` は、`checked` が要素のマークアップに表示されるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-466">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="c3b98-467">@No__t-0 が `true` の場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-467">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="c3b98-468">@No__t-0 が `false` の場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-468">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="c3b98-469">詳細については、「<xref:mvc/views/razor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-469">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="c3b98-470">[Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)などの一部の HTML 属性は、.net 型が `bool` の場合、正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-470">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="c3b98-471">そのような場合は、`bool` ではなく、@no__t 0 の型を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-471">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="c3b98-472">未加工の HTML</span><span class="sxs-lookup"><span data-stu-id="c3b98-472">Raw HTML</span></span>

<span data-ttu-id="c3b98-473">通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-473">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="c3b98-474">生の HTML をレンダリングするには、HTML コンテンツを @no__t 0 値で囲みます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-474">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="c3b98-475">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-475">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="c3b98-476">信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-476">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="c3b98-477">次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリング出力に静的 HTML コンテンツのブロックを追加しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-477">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="c3b98-478">テンプレートコンポーネント</span><span class="sxs-lookup"><span data-stu-id="c3b98-478">Templated components</span></span>

<span data-ttu-id="c3b98-479">テンプレートコンポーネントは、1つまたは複数の UI テンプレートをパラメーターとして受け取り、コンポーネントのレンダリングロジックの一部として使用できるコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-479">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="c3b98-480">テンプレート化されたコンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-480">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="c3b98-481">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-481">A couple of examples include:</span></span>

* <span data-ttu-id="c3b98-482">テーブルのヘッダー、行、およびフッターのテンプレートをユーザーが指定できるようにするテーブルコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="c3b98-482">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="c3b98-483">リスト内の項目を表示するためのテンプレートをユーザーが指定できるようにするリストコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="c3b98-483">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="c3b98-484">テンプレート パラメーター</span><span class="sxs-lookup"><span data-stu-id="c3b98-484">Template parameters</span></span>

<span data-ttu-id="c3b98-485">@No__t-0 または `RenderFragment<T>` の型の1つ以上のコンポーネントパラメーターを指定して、テンプレート化されたコンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-485">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="c3b98-486">レンダーフラグメントは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-486">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="c3b98-487">`RenderFragment<T>` は、render フラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-487">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="c3b98-488">`TableTemplate` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c3b98-488">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="c3b98-489">テンプレート化されたコンポーネントを使用する場合は、パラメーターの名前と一致する子要素を使用してテンプレートパラメーターを指定できます (次の例では `TableHeader` および `RowTemplate`)。</span><span class="sxs-lookup"><span data-stu-id="c3b98-489">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="c3b98-490">テンプレートコンテキストパラメーター</span><span class="sxs-lookup"><span data-stu-id="c3b98-490">Template context parameters</span></span>

<span data-ttu-id="c3b98-491">要素として渡された型 @no__t 0 のコンポーネント引数には、`context` という名前の暗黙的なパラメーターがあります (たとえば、上記のコードサンプルでは `@context.PetId`)。ただし、子要素の `Context` 属性を使用してパラメーター名を変更できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-491">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="c3b98-492">次の例では、`RowTemplate` 要素の @no__t 属性で `pet` パラメーターを指定しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-492">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="c3b98-493">または、component 要素に `Context` 属性を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-493">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="c3b98-494">指定された `Context` 属性は、指定されたすべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-494">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="c3b98-495">これは、暗黙的な子コンテンツ (ラップする子要素を持たない) のコンテンツパラメーター名を指定する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-495">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="c3b98-496">次の例では、`Context` 属性が `TableTemplate` 要素に表示され、すべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-496">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="c3b98-497">汎用型のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="c3b98-497">Generic-typed components</span></span>

<span data-ttu-id="c3b98-498">多くの場合、テンプレート化されたコンポーネントは一般的に型指定されます</span><span class="sxs-lookup"><span data-stu-id="c3b98-498">Templated components are often generically typed.</span></span> <span data-ttu-id="c3b98-499">たとえば、汎用 `ListViewTemplate` コンポーネントを使用して、`IEnumerable<T>` の値を表示できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-499">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="c3b98-500">ジェネリックコンポーネントを定義するには、 [@typeparam](xref:mvc/views/razor#typeparam)ディレクティブを使用して、型パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-500">To define a generic component, use the [@typeparam](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="c3b98-501">ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-501">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="c3b98-502">それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-502">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="c3b98-503">次の例では、`TItem="Pet"` は型を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-503">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="c3b98-504">カスケード値とパラメーター</span><span class="sxs-lookup"><span data-stu-id="c3b98-504">Cascading values and parameters</span></span>

<span data-ttu-id="c3b98-505">場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-505">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="c3b98-506">カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-506">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="c3b98-507">カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-507">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="c3b98-508">テーマの例</span><span class="sxs-lookup"><span data-stu-id="c3b98-508">Theme example</span></span>

<span data-ttu-id="c3b98-509">サンプルアプリの次の例では、`ThemeInfo` クラスは、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-509">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="c3b98-510">*UiThemeInfo/* :</span><span class="sxs-lookup"><span data-stu-id="c3b98-510">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="c3b98-511">先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-511">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="c3b98-512">@No__t-0 コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-512">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="c3b98-513">たとえば、サンプルアプリでは、アプリのレイアウトの1つで、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとしてテーマ情報 (`ThemeInfo`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-513">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="c3b98-514">`ButtonClass` には、レイアウトコンポーネントで `btn-success` の値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-514">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="c3b98-515">すべての子孫コンポーネントは、@no__t 0 のカスケードオブジェクトを介してこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-515">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="c3b98-516">`CascadingValuesParametersLayout` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c3b98-516">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="c3b98-517">カスケード値を使用するために、コンポーネントは `[CascadingParameter]` 属性を使用してカスケード型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-517">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="c3b98-518">カスケード値は、型によってカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-518">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="c3b98-519">サンプルアプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` のカスケード値がカスケードパラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-519">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="c3b98-520">パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-520">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="c3b98-521">`CascadingValuesParametersTheme` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c3b98-521">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="c3b98-522">同じサブツリー内で同じ型の複数の値を連鎖させるには、各 @no__t コンポーネントに一意の @no__t 0 文字列を指定し、対応する `CascadingParameter` を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-522">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="c3b98-523">次の例では、2つの `CascadingValue` コンポーネントが、名前によって `MyCascadingType` の異なるインスタンスをカスケードしています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-523">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="c3b98-524">子孫コンポーネントでは、カスケードされたパラメーターは、先祖コンポーネントの対応するカスケード値から名前で値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-524">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="c3b98-525">TabSet の例</span><span class="sxs-lookup"><span data-stu-id="c3b98-525">TabSet example</span></span>

<span data-ttu-id="c3b98-526">カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-526">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="c3b98-527">たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-527">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="c3b98-528">このサンプルアプリには、タブに実装されている @no__t 0 のインターフェイスがあります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-528">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="c3b98-529">@No__t-0 コンポーネントは `TabSet` コンポーネントを使用します。これには、いくつかの @no__t 2 つのコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-529">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="c3b98-530">子 `Tab` コンポーネントは、パラメーターとして `TabSet` に明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-530">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="c3b98-531">代わりに、子 `Tab` のコンポーネントは、`TabSet` の子コンテンツの一部になります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-531">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="c3b98-532">ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、@no__t 2 のコンポーネントは*それ自体をカスケード値として提供*し、その後、子孫の `Tab` コンポーネントによって取得されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-532">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="c3b98-533">`TabSet` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c3b98-533">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="c3b98-534">子孫の `Tab` コンポーネントは、それ @no__t を含むをカスケードパラメーターとしてキャプチャします。そのため、@no__t 2 つのコンポーネントは、アクティブなタブの @no__t に追加します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-534">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="c3b98-535">`Tab` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c3b98-535">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="c3b98-536">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="c3b98-536">Razor templates</span></span>

<span data-ttu-id="c3b98-537">レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-537">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="c3b98-538">Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-538">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="c3b98-539">次の例は、`RenderFragment` と `RenderFragment<T>` の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-539">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="c3b98-540">レンダーフラグメントは、[テンプレートコンポーネント](#templated-components)に引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-540">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="c3b98-541">前のコードの出力をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-541">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="c3b98-542">手動の RenderTreeBuilder ロジック</span><span class="sxs-lookup"><span data-stu-id="c3b98-542">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="c3b98-543">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` は、コンポーネントと要素を操作するためのメソッドを提供C#します。これには、コードでのコンポーネントの手動作成も含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-543">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="c3b98-544">@No__t-0 を使用してコンポーネントを作成することは高度なシナリオです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-544">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="c3b98-545">形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-545">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="c3b98-546">次の `PetDetails` コンポーネントを考えてみます。このコンポーネントは、手動で別のコンポーネントに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-546">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="c3b98-547">次の例では、`CreateComponent` メソッドのループによって、3つの `PetDetails` コンポーネントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-547">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="c3b98-548">@No__t-0 のメソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-548">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="c3b98-549">Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-549">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="c3b98-550">@No__t 0 のメソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-550">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="c3b98-551">**計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。**</span><span class="sxs-lookup"><span data-stu-id="c3b98-551">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="c3b98-552">詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-552">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="c3b98-553">`BuiltContent` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c3b98-553">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> <span data-ttu-id="c3b98-554">!要する@No__t_0 の型により、レンダリング操作の*結果*を処理できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-554">![WARNING] The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="c3b98-555">これらは、Blazor framework 実装の内部的な詳細です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-555">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="c3b98-556">これらの型は*不安定*であると見なされ、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-556">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="c3b98-557">シーケンス番号は、実行順序ではなくコード行番号に関連します</span><span class="sxs-lookup"><span data-stu-id="c3b98-557">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="c3b98-558">Blazor `.razor` ファイルは常にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-558">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="c3b98-559">コンパイル手順を使用すると、実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、@no__t にとっては大きな利点となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-559">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="c3b98-560">これらの機能強化の主要な例には、*シーケンス番号*が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-560">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="c3b98-561">シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-561">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="c3b98-562">ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-562">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="c3b98-563">次の単純な `.razor` ファイルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="c3b98-563">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="c3b98-564">上記のコードは、次のようにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-564">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="c3b98-565">コードを初めて実行する場合、`someFlag` が `true` の場合、ビルダーは次のメッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-565">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="c3b98-566">シーケンス</span><span class="sxs-lookup"><span data-stu-id="c3b98-566">Sequence</span></span> | <span data-ttu-id="c3b98-567">[種類]</span><span class="sxs-lookup"><span data-stu-id="c3b98-567">Type</span></span>      | <span data-ttu-id="c3b98-568">データ</span><span class="sxs-lookup"><span data-stu-id="c3b98-568">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c3b98-569">0</span><span class="sxs-lookup"><span data-stu-id="c3b98-569">0</span></span>        | <span data-ttu-id="c3b98-570">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="c3b98-570">Text node</span></span> | <span data-ttu-id="c3b98-571">First</span><span class="sxs-lookup"><span data-stu-id="c3b98-571">First</span></span>  |
| <span data-ttu-id="c3b98-572">1</span><span class="sxs-lookup"><span data-stu-id="c3b98-572">1</span></span>        | <span data-ttu-id="c3b98-573">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="c3b98-573">Text node</span></span> | <span data-ttu-id="c3b98-574">Second</span><span class="sxs-lookup"><span data-stu-id="c3b98-574">Second</span></span> |

<span data-ttu-id="c3b98-575">@No__t-0 が @no__t になり、マークアップが再び表示されることを想像してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-575">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="c3b98-576">この時点で、ビルダーは次のものを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-576">This time, the builder receives:</span></span>

| <span data-ttu-id="c3b98-577">シーケンス</span><span class="sxs-lookup"><span data-stu-id="c3b98-577">Sequence</span></span> | <span data-ttu-id="c3b98-578">[種類]</span><span class="sxs-lookup"><span data-stu-id="c3b98-578">Type</span></span>       | <span data-ttu-id="c3b98-579">データ</span><span class="sxs-lookup"><span data-stu-id="c3b98-579">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="c3b98-580">1</span><span class="sxs-lookup"><span data-stu-id="c3b98-580">1</span></span>        | <span data-ttu-id="c3b98-581">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="c3b98-581">Text node</span></span>  | <span data-ttu-id="c3b98-582">Second</span><span class="sxs-lookup"><span data-stu-id="c3b98-582">Second</span></span> |

<span data-ttu-id="c3b98-583">ランタイムが diff を実行すると、シーケンス `0` の項目が削除されたことが認識されるので、次のような単純な*編集スクリプト*が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-583">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="c3b98-584">最初のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-584">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="c3b98-585">プログラムによってシーケンス番号を生成した場合の問題</span><span class="sxs-lookup"><span data-stu-id="c3b98-585">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="c3b98-586">代わりに、次のレンダーツリービルダーのロジックを記述したとします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-586">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="c3b98-587">これで、最初の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-587">Now, the first output is:</span></span>

| <span data-ttu-id="c3b98-588">シーケンス</span><span class="sxs-lookup"><span data-stu-id="c3b98-588">Sequence</span></span> | <span data-ttu-id="c3b98-589">[種類]</span><span class="sxs-lookup"><span data-stu-id="c3b98-589">Type</span></span>      | <span data-ttu-id="c3b98-590">データ</span><span class="sxs-lookup"><span data-stu-id="c3b98-590">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c3b98-591">0</span><span class="sxs-lookup"><span data-stu-id="c3b98-591">0</span></span>        | <span data-ttu-id="c3b98-592">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="c3b98-592">Text node</span></span> | <span data-ttu-id="c3b98-593">First</span><span class="sxs-lookup"><span data-stu-id="c3b98-593">First</span></span>  |
| <span data-ttu-id="c3b98-594">1</span><span class="sxs-lookup"><span data-stu-id="c3b98-594">1</span></span>        | <span data-ttu-id="c3b98-595">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="c3b98-595">Text node</span></span> | <span data-ttu-id="c3b98-596">Second</span><span class="sxs-lookup"><span data-stu-id="c3b98-596">Second</span></span> |

<span data-ttu-id="c3b98-597">この結果は前のケースと同じであるため、負の問題は存在しません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-597">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="c3b98-598">`someFlag` は2番目のレンダリングでは-1 @no__t、出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-598">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="c3b98-599">シーケンス</span><span class="sxs-lookup"><span data-stu-id="c3b98-599">Sequence</span></span> | <span data-ttu-id="c3b98-600">[種類]</span><span class="sxs-lookup"><span data-stu-id="c3b98-600">Type</span></span>      | <span data-ttu-id="c3b98-601">データ</span><span class="sxs-lookup"><span data-stu-id="c3b98-601">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="c3b98-602">0</span><span class="sxs-lookup"><span data-stu-id="c3b98-602">0</span></span>        | <span data-ttu-id="c3b98-603">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="c3b98-603">Text node</span></span> | <span data-ttu-id="c3b98-604">Second</span><span class="sxs-lookup"><span data-stu-id="c3b98-604">Second</span></span> |

<span data-ttu-id="c3b98-605">今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-605">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="c3b98-606">最初のテキストノードの値を `Second` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-606">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="c3b98-607">2番目のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-607">Remove the second text node.</span></span>

<span data-ttu-id="c3b98-608">シーケンス番号を生成すると、@no__t 0 の分岐とループが元のコードに存在する場所に関する有用な情報がすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-608">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="c3b98-609">これにより、diff は前の**2 倍**になります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-609">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="c3b98-610">これは簡単な例です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-610">This is a trivial example.</span></span> <span data-ttu-id="c3b98-611">複雑で深く入れ子になった構造、特にループを使用したより現実的なケースでは、パフォーマンスコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-611">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="c3b98-612">どのループブロックまたは分岐が挿入または削除されたかをすぐに識別するのではなく、diff アルゴリズムでは、レンダリングツリーに深く再帰する必要があります相互に関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-612">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="c3b98-613">ガイダンスと結論</span><span class="sxs-lookup"><span data-stu-id="c3b98-613">Guidance and conclusions</span></span>

* <span data-ttu-id="c3b98-614">シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-614">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="c3b98-615">コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-615">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="c3b98-616">手動で実装された長いブロックを作成しないでください `RenderTreeBuilder` のロジックです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-616">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="c3b98-617">@No__t 0 のファイルを優先し、コンパイラがシーケンス番号を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-617">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="c3b98-618">手動の `RenderTreeBuilder` ロジックを回避できない場合は、長いブロックコードを `OpenRegion` @ no__t @ no__t 呼び出しでラップされた小さな部分に分割します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-618">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="c3b98-619">各リージョンにはシーケンス番号の個別のスペースがあるため、各リージョン内のゼロ (または任意の任意の数) から再起動できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-619">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="c3b98-620">シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-620">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="c3b98-621">初期値とギャップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-621">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="c3b98-622">正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。</span><span class="sxs-lookup"><span data-stu-id="c3b98-622">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="c3b98-623">Blazor はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-623">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="c3b98-624">シーケンス番号が使用されている場合、比較ははるかに高速です。 Blazor には、`.razor` ファイルを作成する開発者に対して、シーケンス番号を自動的に処理するコンパイル手順の利点があります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-624">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="c3b98-625">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="c3b98-625">Localization</span></span>

<span data-ttu-id="c3b98-626">Blazor サーバーアプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-626">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="c3b98-627">ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-627">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="c3b98-628">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-628">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="c3b98-629">クッキー</span><span class="sxs-lookup"><span data-stu-id="c3b98-629">Cookies</span></span>](#cookies)
* [<span data-ttu-id="c3b98-630">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="c3b98-630">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="c3b98-631">使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-631">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="c3b98-632">クッキー</span><span class="sxs-lookup"><span data-stu-id="c3b98-632">Cookies</span></span>

<span data-ttu-id="c3b98-633">ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-633">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="c3b98-634">Cookie は、アプリのホストページ (*Pages/host. cshtml. .cs*) の `OnGet` メソッドによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-634">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="c3b98-635">ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-635">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="c3b98-636">Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c3b98-636">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="c3b98-637">ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-637">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="c3b98-638">したがって、ローカライズカルチャ cookie を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-638">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="c3b98-639">カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-639">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="c3b98-640">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-640">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="c3b98-641">次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-641">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="c3b98-642">Blazor Server アプリで次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-642">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="c3b98-643">ローカライズはアプリで処理されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-643">Localization is handled in the app:</span></span>

1. <span data-ttu-id="c3b98-644">ブラウザーは、アプリに最初の HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-644">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="c3b98-645">カルチャは、ローカリゼーションミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-645">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="c3b98-646">*_Host*の `OnGet` メソッドは、応答の一部として cookie 内のカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-646">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="c3b98-647">ブラウザーは、WebSocket 接続を開き、対話型の Blazor サーバーセッションを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-647">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="c3b98-648">ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-648">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="c3b98-649">Blazor Server セッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-649">The Blazor Server session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="c3b98-650">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="c3b98-650">Provide UI to choose the culture</span></span>

<span data-ttu-id="c3b98-651">ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-651">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="c3b98-652">このプロセスは、ユーザーがセキュリティで保護されたリソース @ no__t にアクセスしようとしたときの web アプリの動作と似ています。ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-652">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="c3b98-653">アプリは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-653">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="c3b98-654">コントローラーは、ユーザーが選択したカルチャを cookie に設定し、ユーザーを元の URI にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="c3b98-654">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="c3b98-655">Cookie でユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するために、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-655">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="c3b98-656">@No__t-0 アクションの結果を使用して、開いているリダイレクト攻撃を防止します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-656">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="c3b98-657">詳細については、「<xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-657">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="c3b98-658">次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-658">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="c3b98-659">Blazor アプリで .NET ローカライズシナリオを使用する</span><span class="sxs-lookup"><span data-stu-id="c3b98-659">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="c3b98-660">Blazor アプリ内では、次の .NET ローカリゼーションとグローバリゼーションのシナリオを利用できます。</span><span class="sxs-lookup"><span data-stu-id="c3b98-660">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="c3b98-661">.NET のリソースシステム</span><span class="sxs-lookup"><span data-stu-id="c3b98-661">.NET's resources system</span></span>
* <span data-ttu-id="c3b98-662">カルチャ固有の数値と日付の書式設定</span><span class="sxs-lookup"><span data-stu-id="c3b98-662">Culture-specific number and date formatting</span></span>

<span data-ttu-id="c3b98-663">Blazor の @no__t 0 機能は、ユーザーの現在のカルチャに基づいてグローバリゼーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="c3b98-663">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="c3b98-664">詳細については、「[データバインディング](#data-binding)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-664">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="c3b98-665">現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-665">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="c3b98-666">Blazor アプリでは、`IStringLocalizer<>`*がサポートされて*います。</span><span class="sxs-lookup"><span data-stu-id="c3b98-666">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="c3b98-667">@no__t 0、`IViewLocalizer<>`、データ注釈のローカライズは MVC シナリオ ASP.NET Core、Blazor アプリではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="c3b98-667">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="c3b98-668">詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3b98-668">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="c3b98-669">スケーラブルベクターグラフィックス (SVG) イメージ</span><span class="sxs-lookup"><span data-stu-id="c3b98-669">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="c3b98-670">Blazor は HTML をレンダリングするため、スケーラブルベクターグラフィックス (svg) イメージ (*svg*) を含むブラウザーでサポートされているイメージは、`<img>` タグでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-670">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="c3b98-671">同様に、SVG イメージは、スタイルシートファイル ( *.css*) の css 規則でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-671">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="c3b98-672">ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-672">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="c3b98-673">コンポーネントファイル (*razor*) に @no__t 0 タグを直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-673">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="c3b98-674">たとえば、@no__t 0 のタグは現在尊重されていないため、`@bind` を一部の SVG タグと共に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="c3b98-674">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="c3b98-675">今後のリリースでは、これらの制限に対処する予定です。</span><span class="sxs-lookup"><span data-stu-id="c3b98-675">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3b98-676">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c3b98-676">Additional resources</span></span>

* <span data-ttu-id="c3b98-677"><xref:security/blazor/server> &ndash; には、リソース枯渇に対処する必要がある Blazor サーバーアプリの構築に関するガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3b98-677"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
