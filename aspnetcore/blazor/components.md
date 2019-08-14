---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/components
ms.openlocfilehash: a95c186d30eaf342f10ecbe6f7add242d4679a0f
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993413"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="75747-103">ASP.NET Core Razor コンポーネントを作成して使用する</span><span class="sxs-lookup"><span data-stu-id="75747-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="75747-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="75747-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="75747-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="75747-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="75747-106">Blazor アプリは*コンポーネント*を使用して構築されます。</span><span class="sxs-lookup"><span data-stu-id="75747-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="75747-107">コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="75747-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="75747-108">コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="75747-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="75747-109">コンポーネントは柔軟で軽量です。</span><span class="sxs-lookup"><span data-stu-id="75747-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="75747-110">入れ子にして再利用したり、プロジェクト間で共有したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="75747-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="75747-111">コンポーネントクラス</span><span class="sxs-lookup"><span data-stu-id="75747-111">Component classes</span></span>

<span data-ttu-id="75747-112">コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。</span><span class="sxs-lookup"><span data-stu-id="75747-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="75747-113">Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。</span><span class="sxs-lookup"><span data-stu-id="75747-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="75747-114">コンポーネントの名前は、大文字で始まる必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="75747-115">たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。</span><span class="sxs-lookup"><span data-stu-id="75747-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="75747-116">`_RazorComponentInclude` MSBuild プロパティを使用してファイルが Razor コンポーネントファイルとして識別されている限り、ファイル拡張子を使用してコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="75747-116">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="75747-117">たとえば、 *Pages*フォルダーにあるすべての*Cshtml*ファイルを Razor コンポーネントファイルとして処理するように指定するアプリを次に示します。</span><span class="sxs-lookup"><span data-stu-id="75747-117">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

<span data-ttu-id="75747-118">コンポーネントの UI は、HTML を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="75747-118">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="75747-119">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="75747-119">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="75747-120">アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="75747-120">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="75747-121">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="75747-121">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="75747-122">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="75747-122">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="75747-123">`@code`ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッドまたはその他のコンポーネントロジックを定義するために指定されます。</span><span class="sxs-lookup"><span data-stu-id="75747-123">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="75747-124">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="75747-124">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="75747-125">ASP.NET Core 3.0 の以前のプレビューで`@functions`は、Razor コンポーネントのブロックと同じ`@code`目的にブロックが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="75747-125">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="75747-126">`@functions`ブロックは`@code` Razor コンポーネントで引き続き機能しますが、ASP.NET Core 3.0 Preview 6 以降ではブロックを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="75747-126">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="75747-127">コンポーネントメンバーは、でC# `@`始まる式を使用して、コンポーネントのレンダリングロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="75747-127">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="75747-128">たとえば、フィールド名C#にプレフィックス`@`を付けることで、フィールドが表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-128">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="75747-129">次の例では、が評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="75747-129">The following example evaluates and renders:</span></span>

* <span data-ttu-id="75747-130">`_headingFontStyle`の`font-style`CSS プロパティ値。</span><span class="sxs-lookup"><span data-stu-id="75747-130">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="75747-131">`_headingText``<h1>`要素の内容。</span><span class="sxs-lookup"><span data-stu-id="75747-131">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="75747-132">コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="75747-132">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="75747-133">Blazor は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="75747-133">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="75747-134">コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="75747-134">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="75747-135">Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="75747-135">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="75747-136">ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="75747-136">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="75747-137">カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を、親コンポーネントまたはアプリのインポートのいずれかの*razor*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="75747-137">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="75747-138">たとえば、次の名前空間は、アプリのルート名前空間が`WebApplication`である場合に、コンポーネントフォルダー内のコンポーネントを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="75747-138">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="75747-139">コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="75747-139">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="75747-140">既存の Razor Pages および MVC アプリでコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-140">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="75747-141">Razor コンポーネントを使用するために既存のページやビューを書き直す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="75747-141">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="75747-142">ページまたはビューが表示されると、コンポーネントは同時に prerendered されます。</span><span class="sxs-lookup"><span data-stu-id="75747-142">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="75747-143">ページまたはビューからコンポーネントを表示するには、 `RenderComponentAsync<TComponent>` HTML ヘルパーメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="75747-144">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="75747-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="75747-145">コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="75747-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="75747-146">コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="75747-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="75747-147">コンポーネントがどのようにレンダリングされ、コンポーネントの状態が Blazor サーバー側アプリで管理されるか<xref:blazor/hosting-models>の詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-147">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="75747-148">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="75747-148">Use components</span></span>

<span data-ttu-id="75747-149">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="75747-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="75747-150">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="75747-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="75747-151">属性のバインドでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="75747-151">Attribute binding is case sensitive.</span></span> <span data-ttu-id="75747-152">たとえば、 `@bind`は有効`@Bind`で、が無効です。</span><span class="sxs-lookup"><span data-stu-id="75747-152">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="75747-153">インデックスの次のマークアップは、インスタンス`HeadingComponent`をレンダリングし*ます。*</span><span class="sxs-lookup"><span data-stu-id="75747-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="75747-154">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="75747-154">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="75747-155">コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="75747-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="75747-156">コンポーネントの名前空間にステートメントを追加すると、コンポーネントが使用可能になり、警告が削除されます。`@using`</span><span class="sxs-lookup"><span data-stu-id="75747-156">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="75747-157">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="75747-157">Component parameters</span></span>

<span data-ttu-id="75747-158">コンポーネントは、コンポーネントクラス`[Parameter]`のパブリックプロパティを使用して定義されているコンポーネントパラメーターを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="75747-158">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="75747-159">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="75747-159">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="75747-160">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="75747-160">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="75747-161">次の例`ParentComponent`では、はの`Title` `ChildComponent`プロパティの値を設定します。</span><span class="sxs-lookup"><span data-stu-id="75747-161">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="75747-162">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="75747-162">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="75747-163">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="75747-163">Child content</span></span>

<span data-ttu-id="75747-164">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="75747-164">Components can set the content of another component.</span></span> <span data-ttu-id="75747-165">割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。</span><span class="sxs-lookup"><span data-stu-id="75747-165">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="75747-166">次の例`ChildComponent`では、にを`ChildContent`表す`RenderFragment`プロパティがあります。これは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="75747-166">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="75747-167">の`ChildContent`値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップに配置されます。</span><span class="sxs-lookup"><span data-stu-id="75747-167">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="75747-168">の`ChildContent`値は、親コンポーネントから受信され、ブートストラップパネルの`panel-body`内に表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-168">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="75747-169">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="75747-169">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="75747-170">コンテンツを`RenderFragment`受け取るプロパティは、規約に`ChildContent`よって名前が付けられている必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-170">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="75747-171">次の`ParentComponent`例では、 `<ChildComponent>`タグ内`ChildComponent`にコンテンツを配置することで、を表示するためのコンテンツを提供できます。</span><span class="sxs-lookup"><span data-stu-id="75747-171">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="75747-172">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="75747-172">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="75747-173">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="75747-173">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="75747-174">コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。</span><span class="sxs-lookup"><span data-stu-id="75747-174">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="75747-175">追加の属性は、ディクショナリ内でキャプチャしてから、 [@attributes](xref:mvc/views/razor#attributes) Razor ディレクティブを使用してコンポーネントがレンダリングされるときに要素に splatted ことができます。</span><span class="sxs-lookup"><span data-stu-id="75747-175">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="75747-176">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="75747-176">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="75747-177">たとえば、多くのパラメーターをサポートするでは`<input>` 、属性を個別に定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="75747-177">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="75747-178">次の例では、最初`<input>`の要素`id="useIndividualParams"`() は個々のコンポーネントパラメーターを使用`<input>`し、`id="useAttributesDict"`2 番目の要素 () は属性スプラッティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-178">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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
            { "required", "true" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="75747-179">パラメーターの型は、文字列キー `IEnumerable<KeyValuePair<string, object>>`を使用して実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-179">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="75747-180">この`IReadOnlyDictionary<string, object>`シナリオでは、を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="75747-180">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="75747-181">両方の`<input>`方法を使用して表示される要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="75747-181">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="true"
       size="50">
```

<span data-ttu-id="75747-182">任意の属性を受け入れるには、プロパティが`[Parameter]` `CaptureUnmatchedValues`に`true`設定された属性を使用して、コンポーネントのパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="75747-182">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedAttributes = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="75747-183">`CaptureUnmatchedValues` の`[Parameter]`プロパティを使用すると、パラメーターは、他のどのパラメーターとも一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="75747-183">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="75747-184">コンポーネントは、で`CaptureUnmatchedValues`1 つのパラメーターのみを定義できます。</span><span class="sxs-lookup"><span data-stu-id="75747-184">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="75747-185">で`CaptureUnmatchedValues`使用されるプロパティ型は、文字列`Dictionary<string, object>`キーを使用してから割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-185">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="75747-186">`IEnumerable<KeyValuePair<string, object>>`また`IReadOnlyDictionary<string, object>`は、このシナリオのオプションでもあります。</span><span class="sxs-lookup"><span data-stu-id="75747-186">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="75747-187">データ バインディング</span><span class="sxs-lookup"><span data-stu-id="75747-187">Data binding</span></span>

<span data-ttu-id="75747-188">コンポーネントと DOM 要素の両方に対するデータバインディングは、 [@bind](xref:mvc/views/razor#bind)属性を使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="75747-188">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="75747-189">次の例では`_italicsCheck` 、フィールドをチェックボックスの checked 状態にバインドします。</span><span class="sxs-lookup"><span data-stu-id="75747-189">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="75747-190">チェックボックスをオンまたはオフにすると、プロパティの値がそれぞれ`true`と`false`に更新されます。</span><span class="sxs-lookup"><span data-stu-id="75747-190">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="75747-191">このチェックボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。</span><span class="sxs-lookup"><span data-stu-id="75747-191">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="75747-192">イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、通常、プロパティの更新は UI に直ちに反映されます。</span><span class="sxs-lookup"><span data-stu-id="75747-192">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="75747-193">を`@bind` `CurrentValue`プロパティ(`<input @bind="CurrentValue" />`) と共に使用することは、基本的に次のようになります。</span><span class="sxs-lookup"><span data-stu-id="75747-193">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="75747-194">コンポーネントがレンダリングされると、 `value`入力要素のが`CurrentValue`プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="75747-194">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="75747-195">ユーザーがテキストボックス`onchange`に入力すると、イベントが発生`CurrentValue`し、プロパティは変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="75747-195">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="75747-196">実際には、では、型変換が実行さ`@bind`れるいくつかのケースが処理されるため、コード生成が少し複雑になります。</span><span class="sxs-lookup"><span data-stu-id="75747-196">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="75747-197">原則とし`@bind`て、は、式の現在の値`value`を属性に関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="75747-197">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="75747-198">構文を使用し`onchange`た`@bind`イベント[@bind-value](xref:mvc/views/razor#bind)の処理に加えて、 `event`パラメーター ([@bind-value:event](xref:mvc/views/razor#bind)) を持つ属性を指定することで、プロパティまたはフィールドを他のイベントを使用してバインドできます。</span><span class="sxs-lookup"><span data-stu-id="75747-198">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="75747-199">次の例では`CurrentValue` 、 `oninput`イベントのプロパティをバインドします。</span><span class="sxs-lookup"><span data-stu-id="75747-199">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="75747-200">と`onchange`は異なり、要素がフォーカスを失った`oninput`ときに、テキストボックスの値が変更されたときに発生します。</span><span class="sxs-lookup"><span data-stu-id="75747-200">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="75747-201">**グローバリゼーション**</span><span class="sxs-lookup"><span data-stu-id="75747-201">**Globalization**</span></span>

<span data-ttu-id="75747-202">`@bind`値は、現在のカルチャの規則を使用して表示および解析するように書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="75747-202">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="75747-203">現在のカルチャには、 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="75747-203">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="75747-204">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="75747-204">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="75747-205">上記のフィールド型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="75747-205">The preceding field types:</span></span>

* <span data-ttu-id="75747-206">は、適切なブラウザーベースの書式規則を使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-206">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="75747-207">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="75747-207">Can't contain free-form text.</span></span>
* <span data-ttu-id="75747-208">ブラウザーの実装に基づいてユーザーの操作特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="75747-208">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="75747-209">次のフィールド型には特定の書式要件があり、Blazor では現在サポートされていません。これは、すべての主要なブラウザーでサポートされていないためです。</span><span class="sxs-lookup"><span data-stu-id="75747-209">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="75747-210">`@bind`値を解析および書式設定<xref:System.Globalization.CultureInfo?displayProperty=fullName>するためのを提供するパラメーターをサポートします。`@bind:culture`</span><span class="sxs-lookup"><span data-stu-id="75747-210">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="75747-211">フィールド型`date`および`number`フィールド型を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="75747-211">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="75747-212">`date`と`number`には、必要なカルチャを提供する組み込みの Blazor サポートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="75747-212">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="75747-213">ユーザーのカルチャを設定する方法については、「[ローカリゼーション](#localization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-213">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="75747-214">**書式指定文字列**</span><span class="sxs-lookup"><span data-stu-id="75747-214">**Format strings**</span></span>

<span data-ttu-id="75747-215">データバインディングは、 <xref:System.DateTime>を使用し[@bind:format](xref:mvc/views/razor#bind)て書式指定文字列で動作します。</span><span class="sxs-lookup"><span data-stu-id="75747-215">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="75747-216">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="75747-216">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="75747-217">前のコードでは、 `<input>`要素のフィールド型 (`type`) は既定`text`でに設定されています。</span><span class="sxs-lookup"><span data-stu-id="75747-217">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="75747-218">`@bind:format`は、次の .NET 型のバインドに対してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="75747-218">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="75747-219"><xref:System.DateTime?displayProperty=fullName> ですか。</span><span class="sxs-lookup"><span data-stu-id="75747-219"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="75747-220"><xref:System.DateTimeOffset?displayProperty=fullName> ですか。</span><span class="sxs-lookup"><span data-stu-id="75747-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="75747-221">属性`@bind:format`は、 `<input>`要素`value`のに適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="75747-221">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="75747-222">この形式は、 `onchange`イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="75747-222">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="75747-223">Blazor には日付を`date`書式設定するためのサポートが組み込まれているため、フィールドの種類の形式を指定することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="75747-223">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="75747-224">**コンポーネントのパラメーター**</span><span class="sxs-lookup"><span data-stu-id="75747-224">**Component parameters**</span></span>

<span data-ttu-id="75747-225">バインディング`@bind-{property}`はコンポーネントパラメーターを認識しますが、はコンポーネント間でプロパティ値をバインドできます。</span><span class="sxs-lookup"><span data-stu-id="75747-225">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="75747-226">次の子コンポーネント (`ChildComponent`) には`Year` 、コンポーネントの`YearChanged`パラメーターとコールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="75747-226">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="75747-227">`EventCallback<T>`については、「 [Eventcallback](#eventcallback) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-227">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="75747-228">次の親コンポーネントは`ChildComponent`を使用し`ParentYear` 、親のパラメーターを子`Year`コンポーネントのパラメーターにバインドします。</span><span class="sxs-lookup"><span data-stu-id="75747-228">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="75747-229">を読み込む`ParentComponent`と、次のマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="75747-229">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="75747-230">`ParentYear`のボタン`ParentComponent`を選択`ChildComponent`してプロパティの値を変更すると、のプロパティが更新されます。`Year`</span><span class="sxs-lookup"><span data-stu-id="75747-230">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="75747-231">の`Year`新しい値は、 `ParentComponent`が再度実行されたときに UI に表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-231">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="75747-232">パラメーター `Year`は、 `Year`パラメーターの型と一致する`YearChanged`コンパニオンイベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="75747-232">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="75747-233">慣例により`<ChildComponent @bind-Year="ParentYear" />` 、は基本的には書き込みに相当します。</span><span class="sxs-lookup"><span data-stu-id="75747-233">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="75747-234">一般に、プロパティは、属性を使用して`@bind-property:event` 、対応するイベントハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="75747-234">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="75747-235">たとえば、プロパティ`MyProp`は、次の2つ`MyEventHandler`の属性を使用してにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="75747-235">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="75747-236">イベント処理</span><span class="sxs-lookup"><span data-stu-id="75747-236">Event handling</span></span>

<span data-ttu-id="75747-237">Razor コンポーネントは、イベント処理機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="75747-237">Razor components provide event handling features.</span></span> <span data-ttu-id="75747-238">という名前`on{event}`の HTML 要素属性 (や`onsubmit`など`onclick` ) にデリゲート型の値を指定すると、Razor コンポーネントはその属性の値をイベントハンドラーとして扱います。</span><span class="sxs-lookup"><span data-stu-id="75747-238">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="75747-239">属性の名前は常に[ @on{event}](xref:mvc/views/razor#onevent)に設定されています。</span><span class="sxs-lookup"><span data-stu-id="75747-239">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="75747-240">次のコードは、 `UpdateHeading` UI でボタンが選択されたときにメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="75747-240">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="75747-241">次のコードは、 `CheckChanged` UI でチェックボックスが変更されたときにメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="75747-241">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="75747-242">イベントハンドラーを非同期にして、を<xref:System.Threading.Tasks.Task>返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="75747-242">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="75747-243">を手動で呼び出す`StateHasChanged()`必要はありません。</span><span class="sxs-lookup"><span data-stu-id="75747-243">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="75747-244">例外は、発生するとログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="75747-244">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="75747-245">次の例では`UpdateHeading` 、ボタンが選択されると、が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="75747-245">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="75747-246">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="75747-246">Event argument types</span></span>

<span data-ttu-id="75747-247">イベントによっては、イベント引数の型が許可されます。</span><span class="sxs-lookup"><span data-stu-id="75747-247">For some events, event argument types are permitted.</span></span> <span data-ttu-id="75747-248">これらのイベントの種類のいずれかにアクセスする必要がない場合は、メソッドの呼び出しで必要とされません。</span><span class="sxs-lookup"><span data-stu-id="75747-248">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="75747-249">サポートされている[Uieventargs](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs)を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="75747-249">Supported [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs) are shown in the following table.</span></span>

| <span data-ttu-id="75747-250">イベント</span><span class="sxs-lookup"><span data-stu-id="75747-250">Event</span></span> | <span data-ttu-id="75747-251">クラス</span><span class="sxs-lookup"><span data-stu-id="75747-251">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="75747-252">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="75747-252">Clipboard</span></span> | `UIClipboardEventArgs` |
| <span data-ttu-id="75747-253">抗力</span><span class="sxs-lookup"><span data-stu-id="75747-253">Drag</span></span>  | <span data-ttu-id="75747-254">`UIDragEventArgs`はドラッグアンドドロップ操作中にドラッグしたデータを保持するために使用され`UIDataTransferItem`、1つ以上のを保持できます。 &ndash; `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="75747-254">`UIDragEventArgs` &ndash; `DataTransfer` is used to hold the dragged data during a drag and drop operation and may hold one or more `UIDataTransferItem`.</span></span> <span data-ttu-id="75747-255">`UIDataTransferItem`1つのドラッグデータ項目を表します。</span><span class="sxs-lookup"><span data-stu-id="75747-255">`UIDataTransferItem` represents one drag data item.</span></span> |
| <span data-ttu-id="75747-256">Error</span><span class="sxs-lookup"><span data-stu-id="75747-256">Error</span></span> | `UIErrorEventArgs` |
| <span data-ttu-id="75747-257">フォーカス</span><span class="sxs-lookup"><span data-stu-id="75747-257">Focus</span></span> | <span data-ttu-id="75747-258">`UIFocusEventArgs`には、の`relatedTarget`サポートは含まれていません。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="75747-258">`UIFocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="75747-259">`<input>` の変更</span><span class="sxs-lookup"><span data-stu-id="75747-259">`<input>` change</span></span> | `UIChangeEventArgs` |
| <span data-ttu-id="75747-260">キーボード</span><span class="sxs-lookup"><span data-stu-id="75747-260">Keyboard</span></span> | `UIKeyboardEventArgs` |
| <span data-ttu-id="75747-261">マウス</span><span class="sxs-lookup"><span data-stu-id="75747-261">Mouse</span></span> | `UIMouseEventArgs` |
| <span data-ttu-id="75747-262">マウスポインター</span><span class="sxs-lookup"><span data-stu-id="75747-262">Mouse pointer</span></span> | `UIPointerEventArgs` |
| <span data-ttu-id="75747-263">マウスホイール</span><span class="sxs-lookup"><span data-stu-id="75747-263">Mouse wheel</span></span> | `UIWheelEventArgs` |
| <span data-ttu-id="75747-264">進行状況</span><span class="sxs-lookup"><span data-stu-id="75747-264">Progress</span></span> | `UIProgressEventArgs` |
| <span data-ttu-id="75747-265">タッチ</span><span class="sxs-lookup"><span data-stu-id="75747-265">Touch</span></span> | <span data-ttu-id="75747-266">`UITouchEventArgs`&ndash; タッチ感度デバイス上の単一のコンタクト`UITouchPoint`ポイントを表します。</span><span class="sxs-lookup"><span data-stu-id="75747-266">`UITouchEventArgs` &ndash; `UITouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="75747-267">前の表に示したイベントのプロパティとイベント処理動作の詳細については、「[参照ソースの EventArgs クラス](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-267">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="75747-268">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="75747-268">Lambda expressions</span></span>

<span data-ttu-id="75747-269">ラムダ式を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="75747-269">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="75747-270">多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。</span><span class="sxs-lookup"><span data-stu-id="75747-270">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="75747-271">次の例では、3つの`UpdateHeading`ボタンを作成します。各ボタンは、UI で選択したときにイベント引数 (`UIMouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="75747-271">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="75747-272">ループのループ変数 (`i`) `for`は、ラムダ式内で直接使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="75747-272">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="75747-273">それ以外の場合は、すべて`i`のラムダ式で同じ変数が使用されます。これにより、の値がすべてのラムダで同じになります。</span><span class="sxs-lookup"><span data-stu-id="75747-273">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="75747-274">常にローカル変数 (`buttonNumber`前の例では) で値をキャプチャし、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-274">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="75747-275">EventCallback</span><span class="sxs-lookup"><span data-stu-id="75747-275">EventCallback</span></span>

<span data-ttu-id="75747-276">入れ子になったコンポーネントの一般的なシナリオは、子コンポーネントのイベントが発生&mdash;したときに親コンポーネントのメソッドを実行することです。たとえば、子で`onclick`イベントが発生した場合などです。</span><span class="sxs-lookup"><span data-stu-id="75747-276">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="75747-277">コンポーネント間でイベントを公開するに`EventCallback`は、を使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-277">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="75747-278">親コンポーネントは、コールバックメソッドを子コンポーネントの`EventCallback`に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="75747-278">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="75747-279">サンプル`ChildComponent`アプリのは、サンプルの`ParentComponent`から`EventCallback`デリゲートを`onclick`受け取るように、ボタンのハンドラーがどのように設定されているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="75747-279">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="75747-280">は`EventCallback` 、周辺機器`UIMouseEventArgs`からの`onclick`イベントに適したを使用して型指定されます。</span><span class="sxs-lookup"><span data-stu-id="75747-280">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="75747-281">は`ParentComponent` 、子の`EventCallback<T>`をその`ShowMessage`メソッドに設定します。</span><span class="sxs-lookup"><span data-stu-id="75747-281">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="75747-282">でボタンが選択されると`ChildComponent`、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="75747-282">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="75747-283">`ParentComponent`の`ShowMessage`メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="75747-283">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="75747-284">`messageText`が更新され、 `ParentComponent`に表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-284">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="75747-285">コールバックの`StateHasChanged`メソッド (`ShowMessage`) では、の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="75747-285">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="75747-286">`StateHasChanged`は、子イベントと同様`ParentComponent`に、子内で実行されるイベントハンドラーでコンポーネントのレンダリングがトリガーされるのと同じように、自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="75747-286">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="75747-287">`EventCallback`非同期`EventCallback<T>`デリゲートを許可します。</span><span class="sxs-lookup"><span data-stu-id="75747-287">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="75747-288">`EventCallback<T>`は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="75747-288">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="75747-289">`EventCallback`は弱く型指定され、任意の引数型を許可します。</span><span class="sxs-lookup"><span data-stu-id="75747-289">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="75747-290">`EventCallback`または<xref:System.Threading.Tasks.Task>を使用`InvokeAsync`してを呼び出し、を`EventCallback<T>`待機します。</span><span class="sxs-lookup"><span data-stu-id="75747-290">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="75747-291">イベント`EventCallback`処理`EventCallback<T>`およびバインドコンポーネントのパラメーターには、およびを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-291">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="75747-292">厳密に型指定`EventCallback<T>` `EventCallback`されたを優先します。</span><span class="sxs-lookup"><span data-stu-id="75747-292">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="75747-293">`EventCallback<T>`コンポーネントのユーザーに対して、より適切なエラーフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="75747-293">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="75747-294">他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="75747-294">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="75747-295">コール`EventCallback`バックに値が渡されない場合は、を使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-295">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="75747-296">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="75747-296">Capture references to components</span></span>

<span data-ttu-id="75747-297">コンポーネント参照を使用すると、 `Show`や`Reset`などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。</span><span class="sxs-lookup"><span data-stu-id="75747-297">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="75747-298">コンポーネント参照をキャプチャするには、 [@ref](xref:mvc/views/razor#ref)子コンポーネントに属性を追加し、子コンポーネントと同じ名前と同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="75747-298">To capture a component reference, add a [@ref](xref:mvc/views/razor#ref) attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

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

<span data-ttu-id="75747-299">コンポーネントがレンダリング`loginDialog`されると、 `MyLoginDialog`子コンポーネントのインスタンスがフィールドに設定されます。</span><span class="sxs-lookup"><span data-stu-id="75747-299">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="75747-300">その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="75747-300">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="75747-301">変数は、コンポーネントがレンダリングされた後にのみ設定され`MyLoginDialog` 、その出力には要素が含まれます。 `loginDialog`</span><span class="sxs-lookup"><span data-stu-id="75747-301">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="75747-302">この時点までは、参照するものはありません。</span><span class="sxs-lookup"><span data-stu-id="75747-302">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="75747-303">コンポーネント参照のレンダリングが完了した後にコンポーネント参照を`OnAfterRenderAsync`操作`OnAfterRender`するには、メソッドまたはメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-303">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="75747-304">コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="75747-304">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="75747-305">コンポーネント参照は、.net コードで&mdash;のみ使用されているため、JavaScript コードに渡されません。</span><span class="sxs-lookup"><span data-stu-id="75747-305">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="75747-306">子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="75747-306">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="75747-307">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="75747-307">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="75747-308">通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="75747-308">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="75747-309">キー \@を使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="75747-309">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="75747-310">要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-310">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="75747-311">通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="75747-311">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="75747-312">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="75747-312">Consider the following example:</span></span>

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

<span data-ttu-id="75747-313">`People`コレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75747-313">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="75747-314">コンポーネントのれ`<DetailsEditor>`時に、コンポーネントが異なる`Details`パラメーター値を受け取るように変更されることがあります。</span><span class="sxs-lookup"><span data-stu-id="75747-314">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="75747-315">これにより、予想よりも複雑な再リリースが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75747-315">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="75747-316">場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75747-316">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="75747-317">マッピングプロセスは、 `@key`ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="75747-317">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="75747-318">`@key`比較アルゴリズムによって、キーの値に基づいて要素またはコンポーネントの保持が保証されます。</span><span class="sxs-lookup"><span data-stu-id="75747-318">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="75747-319">コレクションが`People`変更されると、比較アルゴリズムによって`<DetailsEditor>`インスタンスと`person`インスタンスの間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="75747-319">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="75747-320">が一覧から削除されると、対応する`<DetailsEditor>`インスタンスだけが UI から削除されます。 `People` `Person`</span><span class="sxs-lookup"><span data-stu-id="75747-320">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="75747-321">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="75747-321">Other instances are left unchanged.</span></span>
* <span data-ttu-id="75747-322">がリスト内のある位置に挿入されると、その`<DetailsEditor>`位置に1つの新しいインスタンスが挿入されます。 `Person`</span><span class="sxs-lookup"><span data-stu-id="75747-322">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="75747-323">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="75747-323">Other instances are left unchanged.</span></span>
* <span data-ttu-id="75747-324">エントリが順序変更されると、対応`<DetailsEditor>`するインスタンスが UI に保持され、順序が変更されます。 `Person`</span><span class="sxs-lookup"><span data-stu-id="75747-324">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="75747-325">場合によっては、 `@key`を使用すると、回避策の複雑さが最小限に抑えられ、DOM のステートフルな部分 (フォーカス位置など) での潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="75747-325">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="75747-326">キーは、各コンテナー要素またはコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="75747-326">Keys are local to each container element or component.</span></span> <span data-ttu-id="75747-327">キーがドキュメント全体でグローバルに比較されることはありません。</span><span class="sxs-lookup"><span data-stu-id="75747-327">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="75747-328">キーを使用\@する場合</span><span class="sxs-lookup"><span data-stu-id="75747-328">When to use \@key</span></span>

<span data-ttu-id="75747-329">通常は、リストがレンダリングさ`@key`れるたびに (たとえば、 `@foreach`ブロックで) 使用し、を定義するための適切な`@key`値が存在することを意味します。</span><span class="sxs-lookup"><span data-stu-id="75747-329">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="75747-330">また、を使用`@key`して、オブジェクトが変更されたときに、Blazor が要素またはコンポーネントのサブツリーを保持しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="75747-330">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="75747-331">属性`@currentPerson`ディレクティブが変更`@key`された場合、Blazor は、 `<div>`とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築するように強制します。</span><span class="sxs-lookup"><span data-stu-id="75747-331">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="75747-332">これは、変更時に`@currentPerson` UI 状態が保持されないことを保証する必要がある場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="75747-332">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="75747-333">キーを使用\@しない場合</span><span class="sxs-lookup"><span data-stu-id="75747-333">When not to use \@key</span></span>

<span data-ttu-id="75747-334">で`@key`比較すると、パフォーマンスコストが発生します。</span><span class="sxs-lookup"><span data-stu-id="75747-334">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="75747-335">パフォーマンスコストは大きくありませんが、 `@key`要素またはコンポーネントの保存ルールの制御がアプリにメリットをもたらすかどうかを指定するだけです。</span><span class="sxs-lookup"><span data-stu-id="75747-335">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="75747-336">が使用`@key`されていない場合でも、Blazor は子要素とコンポーネントインスタンスを可能な限り保持します。</span><span class="sxs-lookup"><span data-stu-id="75747-336">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="75747-337">を使用する唯一の`@key`利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。</span><span class="sxs-lookup"><span data-stu-id="75747-337">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="75747-338">キーに\@使用する値</span><span class="sxs-lookup"><span data-stu-id="75747-338">What values to use for \@key</span></span>

<span data-ttu-id="75747-339">一般に、には次のいずれかの値を指定するの`@key`が理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="75747-339">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="75747-340">モデルオブジェクトインスタンス ( `Person`たとえば、前の例のインスタンス)。</span><span class="sxs-lookup"><span data-stu-id="75747-340">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="75747-341">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="75747-341">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="75747-342">一意の識別子 (たとえば、 `int` `string`、、または`Guid`型の主キーの値)。</span><span class="sxs-lookup"><span data-stu-id="75747-342">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="75747-343">に使用される値`@key`が競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="75747-343">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="75747-344">競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="75747-344">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="75747-345">個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="75747-345">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="75747-346">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="75747-346">Lifecycle methods</span></span>

<span data-ttu-id="75747-347">`OnInitAsync`を`OnInit`実行し、コンポーネントを初期化するコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="75747-347">`OnInitAsync` and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="75747-348">非同期操作を実行するには`OnInitAsync` 、操作`await`でおよびキーワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-348">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="75747-349">同期操作の場合は、 `OnInit`次のように使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-349">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="75747-350">`OnParametersSetAsync`コンポーネント`OnParametersSet`が親からパラメーターを受け取り、値がプロパティに割り当てられると、とが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="75747-350">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="75747-351">これらのメソッドは、コンポーネントの初期化の後、およびコンポーネントがレンダリングされるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="75747-351">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="75747-352">`OnAfterRenderAsync`と`OnAfterRender`は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="75747-352">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="75747-353">この時点で、要素参照とコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="75747-353">Element and component references are populated at this point.</span></span> <span data-ttu-id="75747-354">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-354">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="75747-355">レンダー時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="75747-355">Handle incomplete async actions at render</span></span>

<span data-ttu-id="75747-356">ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75747-356">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="75747-357">ライフサイクルメソッド`null`の実行中に、オブジェクトがデータと共に読み込まれたり、不完全になったりする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75747-357">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="75747-358">オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="75747-358">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="75747-359">オブジェクトが`null`のときに、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="75747-359">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="75747-360">Blazor テンプレートの`OnInitAsync` `forecasts`コンポーネントでは、はユーザー receive 予測データ () に上書きされます。 `FetchData`</span><span class="sxs-lookup"><span data-stu-id="75747-360">In the `FetchData` component of the Blazor templates, `OnInitAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="75747-361">`forecasts` が`null`の場合、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-361">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="75747-362">によっ`Task`て`OnInitAsync`返されたが完了すると、コンポーネントは更新された状態とピアリングされます。</span><span class="sxs-lookup"><span data-stu-id="75747-362">After the `Task` returned by `OnInitAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="75747-363">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="75747-363">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="75747-364">パラメーターが設定される前にコードを実行する</span><span class="sxs-lookup"><span data-stu-id="75747-364">Execute code before parameters are set</span></span>

<span data-ttu-id="75747-365">`SetParameters`をオーバーライドすると、パラメーターを設定する前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="75747-365">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="75747-366">が`base.SetParameters`呼び出されない場合、カスタムコードは、必要に応じて入力パラメーターの値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="75747-366">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="75747-367">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="75747-367">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="75747-368">UI の更新を抑制する</span><span class="sxs-lookup"><span data-stu-id="75747-368">Suppress refreshing of the UI</span></span>

<span data-ttu-id="75747-369">`ShouldRender`UI の更新を抑制するためにオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="75747-369">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="75747-370">実装からが返さ`true`れた場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="75747-370">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="75747-371">がオーバーライド`ShouldRender`された場合でも、コンポーネントは常に最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-371">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="75747-372">IDisposable によるコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="75747-372">Component disposal with IDisposable</span></span>

<span data-ttu-id="75747-373">コンポーネントがを実装<xref:System.IDisposable>している場合は、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="75747-373">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="75747-374">次のコンポーネントで`@implements IDisposable`は、 `Dispose`メソッドとメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-374">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="75747-375">ルーティング</span><span class="sxs-lookup"><span data-stu-id="75747-375">Routing</span></span>

<span data-ttu-id="75747-376">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。</span><span class="sxs-lookup"><span data-stu-id="75747-376">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="75747-377">`@page`ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラス<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>にルートテンプレートを指定するが与えられます。</span><span class="sxs-lookup"><span data-stu-id="75747-377">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="75747-378">実行時に、ルーターはを`RouteAttribute`使用してコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="75747-378">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="75747-379">コンポーネントには、複数のルートテンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="75747-379">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="75747-380">次のコンポーネントは、と`/BlazorRoute` `/DifferentBlazorRoute`に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="75747-380">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="75747-381">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="75747-381">Route parameters</span></span>

<span data-ttu-id="75747-382">コンポーネントは、 `@page`ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="75747-382">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="75747-383">ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="75747-383">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="75747-384">*ルートパラメーターコンポーネント*:</span><span class="sxs-lookup"><span data-stu-id="75747-384">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="75747-385">省略可能なパラメーターはサポートさ`@page`れていないため、上記の例では2つのディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="75747-385">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="75747-386">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="75747-386">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="75747-387">2番`@page`目のディレクティブ`{text}`は route パラメーターを受け取り、その値`Text`をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="75747-387">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="75747-388">"分離コード" エクスペリエンスの基底クラスの継承</span><span class="sxs-lookup"><span data-stu-id="75747-388">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="75747-389">コンポーネントファイルは、HTML マークC#アップと処理コードを同じファイルに混在させます。</span><span class="sxs-lookup"><span data-stu-id="75747-389">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="75747-390">ディレクティブ`@inherits`を使用すると、コンポーネントマークアップを処理コードから分離する "分離コード" エクスペリエンスを Blazor アプリに提供できます。</span><span class="sxs-lookup"><span data-stu-id="75747-390">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="75747-391">この[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)は、コンポーネントが基本クラス`BlazorRocksBase`を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="75747-391">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="75747-392">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="75747-392">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="75747-393">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="75747-393">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="75747-394">基底クラスはから`ComponentBase`派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-394">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="75747-395">コンポーネントのインポート</span><span class="sxs-lookup"><span data-stu-id="75747-395">Import components</span></span>

<span data-ttu-id="75747-396">Razor を使用して作成されたコンポーネントの名前空間は、次のものに基づいています。</span><span class="sxs-lookup"><span data-stu-id="75747-396">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="75747-397">プロジェクトの`RootNamespace`。</span><span class="sxs-lookup"><span data-stu-id="75747-397">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="75747-398">プロジェクトルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="75747-398">The path from the project root to the component.</span></span> <span data-ttu-id="75747-399">たとえば、 `ComponentsSample/Pages/Index.razor`は名前空間`ComponentsSample.Pages`にあります。</span><span class="sxs-lookup"><span data-stu-id="75747-399">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="75747-400">コンポーネントはC# 、名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="75747-400">Components follow C# name binding rules.</span></span> <span data-ttu-id="75747-401">*インデックス*の場合、同じフォルダー、*ページ*、および親フォルダー内のすべてのコンポーネントがスコープに含まれます。</span><span class="sxs-lookup"><span data-stu-id="75747-401">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="75747-402">別の名前空間で定義されているコンポーネントは、Razor の[ \@using](xref:mvc/views/razor#using)ディレクティブを使用してスコープに入れることができます。</span><span class="sxs-lookup"><span data-stu-id="75747-402">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="75747-403">別のコンポーネント ( `NavMenu.razor`) がフォルダー `ComponentsSample/Shared/`に存在する場合は、次`@using`のステートメント`Index.razor`を使用してでコンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="75747-403">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="75747-404">コンポーネントは、完全修飾名を使用して参照することもできます。これにより、 [ \@using](xref:mvc/views/razor#using)ディレクティブは不要になります。</span><span class="sxs-lookup"><span data-stu-id="75747-404">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="75747-405">この`global::`修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="75747-405">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="75747-406">エイリアス`using`付きステートメント (など) を使用し`@using Foo = Bar`たコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="75747-406">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="75747-407">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="75747-407">Partially qualified names aren't supported.</span></span> <span data-ttu-id="75747-408">たとえば、を使用`@using ComponentsSample`した`NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>`の追加と参照はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="75747-408">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="75747-409">条件付き HTML 要素の属性</span><span class="sxs-lookup"><span data-stu-id="75747-409">Conditional HTML element attributes</span></span>

<span data-ttu-id="75747-410">HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-410">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="75747-411">値がまたは`false` `null`の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="75747-411">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="75747-412">値が`true`の場合、属性は最小化されて表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-412">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="75747-413">次の例では`IsCompleted` 、が`checked`要素のマークアップに表示されるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="75747-413">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="75747-414">`IsCompleted` が`true`の場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-414">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="75747-415">`IsCompleted` が`false`の場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="75747-415">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="75747-416">詳細については、「 <xref:mvc/views/razor> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-416">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="75747-417">未加工の HTML</span><span class="sxs-lookup"><span data-stu-id="75747-417">Raw HTML</span></span>

<span data-ttu-id="75747-418">通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="75747-418">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="75747-419">生の html をレンダリングするには、html コンテンツ`MarkupString`を値にラップします。</span><span class="sxs-lookup"><span data-stu-id="75747-419">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="75747-420">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="75747-420">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="75747-421">信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-421">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="75747-422">次の例では、 `MarkupString`型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加しています。</span><span class="sxs-lookup"><span data-stu-id="75747-422">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="75747-423">テンプレートコンポーネント</span><span class="sxs-lookup"><span data-stu-id="75747-423">Templated components</span></span>

<span data-ttu-id="75747-424">テンプレートコンポーネントは、1つまたは複数の UI テンプレートをパラメーターとして受け取り、コンポーネントのレンダリングロジックの一部として使用できるコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="75747-424">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="75747-425">テンプレート化されたコンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="75747-425">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="75747-426">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="75747-426">A couple of examples include:</span></span>

* <span data-ttu-id="75747-427">テーブルのヘッダー、行、およびフッターのテンプレートをユーザーが指定できるようにするテーブルコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="75747-427">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="75747-428">リスト内の項目を表示するためのテンプレートをユーザーが指定できるようにするリストコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="75747-428">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="75747-429">テンプレート パラメーター</span><span class="sxs-lookup"><span data-stu-id="75747-429">Template parameters</span></span>

<span data-ttu-id="75747-430">テンプレートコンポーネントは、型または`RenderFragment` `RenderFragment<T>`型の1つ以上のコンポーネントパラメーターを指定することによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="75747-430">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="75747-431">レンダーフラグメントは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="75747-431">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="75747-432">`RenderFragment<T>`は、render フラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="75747-432">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="75747-433">`TableTemplate`成分</span><span class="sxs-lookup"><span data-stu-id="75747-433">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="75747-434">テンプレート化されたコンポーネントを使用する場合、テンプレートパラメーターは、パラメーターの名前 (`TableHeader`および`RowTemplate`次の例では) と一致する子要素を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="75747-434">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
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

### <a name="template-context-parameters"></a><span data-ttu-id="75747-435">テンプレートコンテキストパラメーター</span><span class="sxs-lookup"><span data-stu-id="75747-435">Template context parameters</span></span>

<span data-ttu-id="75747-436">要素として`RenderFragment<T>`渡された型のコンポーネント引数に`context`は、という名前の暗黙的なパラメーター `@context.PetId`があります (上記のコードサンプルの例で`Context`は)。ただし、子の属性を使用してパラメーター名を変更できます。element.</span><span class="sxs-lookup"><span data-stu-id="75747-436">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="75747-437">次の例では、 `RowTemplate`要素の`Context`属性によっ`pet`てパラメーターが指定されています。</span><span class="sxs-lookup"><span data-stu-id="75747-437">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
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

<span data-ttu-id="75747-438">または、コンポーネント要素で`Context`属性を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="75747-438">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="75747-439">指定された属性は、指定されたすべてのテンプレートパラメーターに適用されます。`Context`</span><span class="sxs-lookup"><span data-stu-id="75747-439">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="75747-440">これは、暗黙的な子コンテンツ (ラップする子要素を持たない) のコンテンツパラメーター名を指定する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="75747-440">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="75747-441">次の例`Context`では、属性が`TableTemplate`要素に表示され、すべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="75747-441">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

### <a name="generic-typed-components"></a><span data-ttu-id="75747-442">汎用型のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="75747-442">Generic-typed components</span></span>

<span data-ttu-id="75747-443">多くの場合、テンプレート化されたコンポーネントは一般的に型指定されます</span><span class="sxs-lookup"><span data-stu-id="75747-443">Templated components are often generically typed.</span></span> <span data-ttu-id="75747-444">たとえば、ジェネリック`ListViewTemplate`コンポーネントを使用して値を表示`IEnumerable<T>`できます。</span><span class="sxs-lookup"><span data-stu-id="75747-444">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="75747-445">ジェネリックコンポーネントを定義するには、 `@typeparam`ディレクティブを使用して型パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="75747-445">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="75747-446">ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。</span><span class="sxs-lookup"><span data-stu-id="75747-446">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="75747-447">それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-447">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="75747-448">次の例では`TItem="Pet"` 、型を指定します。</span><span class="sxs-lookup"><span data-stu-id="75747-448">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="75747-449">カスケード値とパラメーター</span><span class="sxs-lookup"><span data-stu-id="75747-449">Cascading values and parameters</span></span>

<span data-ttu-id="75747-450">場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="75747-450">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="75747-451">カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="75747-451">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="75747-452">カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。</span><span class="sxs-lookup"><span data-stu-id="75747-452">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="75747-453">テーマの例</span><span class="sxs-lookup"><span data-stu-id="75747-453">Theme example</span></span>

<span data-ttu-id="75747-454">次のサンプルアプリの例では、クラス`ThemeInfo`は、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。</span><span class="sxs-lookup"><span data-stu-id="75747-454">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="75747-455">*UiThemeInfo/* :</span><span class="sxs-lookup"><span data-stu-id="75747-455">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="75747-456">先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="75747-456">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="75747-457">コンポーネント`CascadingValue`は、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="75747-457">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="75747-458">たとえば、サンプルアプリでは、`ThemeInfo` `@Body`プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとして、アプリのいずれかのレイアウトでテーマ情報 () を指定します。</span><span class="sxs-lookup"><span data-stu-id="75747-458">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="75747-459">`ButtonClass`レイアウトコンポーネントにの`btn-success`値が割り当てられています。</span><span class="sxs-lookup"><span data-stu-id="75747-459">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="75747-460">すべての子孫コンポーネントは、 `ThemeInfo`カスケードオブジェクトを介してこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="75747-460">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="75747-461">`CascadingValuesParametersLayout`成分</span><span class="sxs-lookup"><span data-stu-id="75747-461">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
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

<span data-ttu-id="75747-462">カスケード値を使用するために、コンポーネントは、 `[CascadingParameter]`属性を使用するか文字列名の値に基づいて、カスケード型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="75747-462">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="75747-463">同じ型のカスケード値が複数あり、同じサブツリー内で区別する必要がある場合は、文字列名値を使用したバインドが関係します。</span><span class="sxs-lookup"><span data-stu-id="75747-463">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="75747-464">カスケード値は、型によってカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="75747-464">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="75747-465">サンプルアプリ`CascadingValuesParametersTheme`では、カスケード値が`ThemeInfo`カスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="75747-465">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="75747-466">パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="75747-466">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="75747-467">`CascadingValuesParametersTheme`成分</span><span class="sxs-lookup"><span data-stu-id="75747-467">`CascadingValuesParametersTheme` component:</span></span>

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

### <a name="tabset-example"></a><span data-ttu-id="75747-468">TabSet の例</span><span class="sxs-lookup"><span data-stu-id="75747-468">TabSet example</span></span>

<span data-ttu-id="75747-469">カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。</span><span class="sxs-lookup"><span data-stu-id="75747-469">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="75747-470">たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="75747-470">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="75747-471">このサンプルアプリには`ITab` 、タブによって実装されるインターフェイスがあります。</span><span class="sxs-lookup"><span data-stu-id="75747-471">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="75747-472">コンポーネント`CascadingValuesParametersTabSet`は、次`TabSet`のコンポーネントを含む`Tab`コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="75747-472">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="75747-473">子`Tab`コンポーネントは、 `TabSet`パラメーターとしてに明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="75747-473">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="75747-474">代わりに、子`Tab`コンポーネントはの子コンテンツ`TabSet`の一部になります。</span><span class="sxs-lookup"><span data-stu-id="75747-474">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="75747-475">ただし、で`TabSet`は、ヘッダーとアクティブな`Tab`タブをレンダリングできるように、各コンポーネントについて認識する必要があります。追加のコードを必要とせずにこの`TabSet`調整を可能にするために、コンポーネントは、子孫`Tab`コンポーネントによって取得される*カスケード値としてそれ自体を提供でき*ます。</span><span class="sxs-lookup"><span data-stu-id="75747-475">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="75747-476">`TabSet`成分</span><span class="sxs-lookup"><span data-stu-id="75747-476">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="75747-477">子孫`Tab`コンポーネントは、を含む`TabSet`をカスケード型パラメーターとして`Tab`キャプチャします。これにより、コンポーネントは、 `TabSet`どのタブがアクティブであるかを座標に追加します。</span><span class="sxs-lookup"><span data-stu-id="75747-477">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="75747-478">`Tab`成分</span><span class="sxs-lookup"><span data-stu-id="75747-478">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="75747-479">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="75747-479">Razor templates</span></span>

<span data-ttu-id="75747-480">レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="75747-480">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="75747-481">Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="75747-481">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="75747-482">次の例は、と`RenderFragment` `RenderFragment<T>`の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="75747-482">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="75747-483">レンダーフラグメントは、[テンプレートコンポーネント](#templated-components)に引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="75747-483">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="75747-484">前のコードの出力をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="75747-484">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="75747-485">手動の RenderTreeBuilder ロジック</span><span class="sxs-lookup"><span data-stu-id="75747-485">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="75747-486">`Microsoft.AspNetCore.Components.RenderTree`コンポーネントと要素を操作するためのメソッドを提供しますC# 。これには、コードでのコンポーネントの手動作成も含まれます。</span><span class="sxs-lookup"><span data-stu-id="75747-486">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="75747-487">を使用してコンポーネントを作成することは高度なシナリオです。`RenderTreeBuilder`</span><span class="sxs-lookup"><span data-stu-id="75747-487">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="75747-488">形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75747-488">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="75747-489">別のコンポーネント`PetDetails`に手動で組み込むことができる次のコンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="75747-489">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="75747-490">次の例では、 `CreateComponent`メソッド内のループによって3つ`PetDetails`のコンポーネントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="75747-490">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="75747-491">メソッドを`RenderTreeBuilder`呼び出してコンポーネント (`OpenComponent`および`AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。</span><span class="sxs-lookup"><span data-stu-id="75747-491">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="75747-492">Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。</span><span class="sxs-lookup"><span data-stu-id="75747-492">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="75747-493">メソッドを使用して`RenderTreeBuilder`コンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。</span><span class="sxs-lookup"><span data-stu-id="75747-493">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="75747-494">**計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。**</span><span class="sxs-lookup"><span data-stu-id="75747-494">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="75747-495">詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-495">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="75747-496">`BuiltContent`成分</span><span class="sxs-lookup"><span data-stu-id="75747-496">`BuiltContent` component:</span></span>

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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="75747-497">シーケンス番号は、実行順序ではなくコード行番号に関連します</span><span class="sxs-lookup"><span data-stu-id="75747-497">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="75747-498">Blazor `.razor`ファイルは常にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="75747-498">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="75747-499">コンパイル手順を使用すると、 `.razor`実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、これはにとって大きな利点となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75747-499">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="75747-500">これらの機能強化の主要な例には、*シーケンス番号*が含まれます。</span><span class="sxs-lookup"><span data-stu-id="75747-500">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="75747-501">シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。</span><span class="sxs-lookup"><span data-stu-id="75747-501">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="75747-502">ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。</span><span class="sxs-lookup"><span data-stu-id="75747-502">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="75747-503">次の単純な`.razor`ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="75747-503">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="75747-504">上記のコードは、次のようにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="75747-504">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="75747-505">コードを初めて実行するときは、が`someFlag` `true`の場合、ビルダーは次のメッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="75747-505">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="75747-506">シーケンス</span><span class="sxs-lookup"><span data-stu-id="75747-506">Sequence</span></span> | <span data-ttu-id="75747-507">種類</span><span class="sxs-lookup"><span data-stu-id="75747-507">Type</span></span>      | <span data-ttu-id="75747-508">データ</span><span class="sxs-lookup"><span data-stu-id="75747-508">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="75747-509">0</span><span class="sxs-lookup"><span data-stu-id="75747-509">0</span></span>        | <span data-ttu-id="75747-510">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="75747-510">Text node</span></span> | <span data-ttu-id="75747-511">First</span><span class="sxs-lookup"><span data-stu-id="75747-511">First</span></span>  |
| <span data-ttu-id="75747-512">1</span><span class="sxs-lookup"><span data-stu-id="75747-512">1</span></span>        | <span data-ttu-id="75747-513">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="75747-513">Text node</span></span> | <span data-ttu-id="75747-514">Second</span><span class="sxs-lookup"><span data-stu-id="75747-514">Second</span></span> |

<span data-ttu-id="75747-515">がに`someFlag`なり`false`、マークアップが再び表示されるとします。</span><span class="sxs-lookup"><span data-stu-id="75747-515">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="75747-516">この時点で、ビルダーは次のものを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="75747-516">This time, the builder receives:</span></span>

| <span data-ttu-id="75747-517">シーケンス</span><span class="sxs-lookup"><span data-stu-id="75747-517">Sequence</span></span> | <span data-ttu-id="75747-518">種類</span><span class="sxs-lookup"><span data-stu-id="75747-518">Type</span></span>       | <span data-ttu-id="75747-519">データ</span><span class="sxs-lookup"><span data-stu-id="75747-519">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="75747-520">1</span><span class="sxs-lookup"><span data-stu-id="75747-520">1</span></span>        | <span data-ttu-id="75747-521">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="75747-521">Text node</span></span>  | <span data-ttu-id="75747-522">Second</span><span class="sxs-lookup"><span data-stu-id="75747-522">Second</span></span> |

<span data-ttu-id="75747-523">ランタイムが diff を実行すると、シーケンス`0`の項目が削除されたことが認識されるため、次のような単純な*編集スクリプト*が生成されます。</span><span class="sxs-lookup"><span data-stu-id="75747-523">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="75747-524">最初のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="75747-524">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="75747-525">プログラムによってシーケンス番号を生成した場合の問題</span><span class="sxs-lookup"><span data-stu-id="75747-525">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="75747-526">代わりに、次のレンダーツリービルダーのロジックを記述したとします。</span><span class="sxs-lookup"><span data-stu-id="75747-526">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="75747-527">これで、最初の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="75747-527">Now, the first output is:</span></span>

| <span data-ttu-id="75747-528">シーケンス</span><span class="sxs-lookup"><span data-stu-id="75747-528">Sequence</span></span> | <span data-ttu-id="75747-529">種類</span><span class="sxs-lookup"><span data-stu-id="75747-529">Type</span></span>      | <span data-ttu-id="75747-530">データ</span><span class="sxs-lookup"><span data-stu-id="75747-530">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="75747-531">0</span><span class="sxs-lookup"><span data-stu-id="75747-531">0</span></span>        | <span data-ttu-id="75747-532">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="75747-532">Text node</span></span> | <span data-ttu-id="75747-533">First</span><span class="sxs-lookup"><span data-stu-id="75747-533">First</span></span>  |
| <span data-ttu-id="75747-534">1</span><span class="sxs-lookup"><span data-stu-id="75747-534">1</span></span>        | <span data-ttu-id="75747-535">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="75747-535">Text node</span></span> | <span data-ttu-id="75747-536">Second</span><span class="sxs-lookup"><span data-stu-id="75747-536">Second</span></span> |

<span data-ttu-id="75747-537">この結果は前のケースと同じであるため、負の問題は存在しません。</span><span class="sxs-lookup"><span data-stu-id="75747-537">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="75747-538">`someFlag`2番目のレンダリングで、出力は次のようになります。`false`</span><span class="sxs-lookup"><span data-stu-id="75747-538">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="75747-539">シーケンス</span><span class="sxs-lookup"><span data-stu-id="75747-539">Sequence</span></span> | <span data-ttu-id="75747-540">種類</span><span class="sxs-lookup"><span data-stu-id="75747-540">Type</span></span>      | <span data-ttu-id="75747-541">データ</span><span class="sxs-lookup"><span data-stu-id="75747-541">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="75747-542">0</span><span class="sxs-lookup"><span data-stu-id="75747-542">0</span></span>        | <span data-ttu-id="75747-543">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="75747-543">Text node</span></span> | <span data-ttu-id="75747-544">Second</span><span class="sxs-lookup"><span data-stu-id="75747-544">Second</span></span> |

<span data-ttu-id="75747-545">今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="75747-545">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="75747-546">最初のテキストノードの値をに`Second`変更します。</span><span class="sxs-lookup"><span data-stu-id="75747-546">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="75747-547">2番目のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="75747-547">Remove the second text node.</span></span>

<span data-ttu-id="75747-548">シーケンス番号を生成すると、元のコードに分岐と`if/else`ループが存在する場所に関する有用な情報がすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="75747-548">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="75747-549">これにより、diff は前の**2 倍**になります。</span><span class="sxs-lookup"><span data-stu-id="75747-549">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="75747-550">これは簡単な例です。</span><span class="sxs-lookup"><span data-stu-id="75747-550">This is a trivial example.</span></span> <span data-ttu-id="75747-551">複雑で深く入れ子になった構造、特にループを使用したより現実的なケースでは、パフォーマンスコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="75747-551">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="75747-552">どのループブロックまたは分岐が挿入または削除されたかをすぐに識別するのではなく、diff アルゴリズムでは、レンダリングツリーに深く再帰する必要があります相互に関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="75747-552">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="75747-553">ガイダンスと結論</span><span class="sxs-lookup"><span data-stu-id="75747-553">Guidance and conclusions</span></span>

* <span data-ttu-id="75747-554">シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="75747-554">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="75747-555">コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="75747-555">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="75747-556">手動で実装`RenderTreeBuilder`されたロジックの長いブロックは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="75747-556">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="75747-557">ファイル`.razor`を優先し、コンパイラがシーケンス番号を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="75747-557">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="75747-558">シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="75747-558">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="75747-559">初期値とギャップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="75747-559">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="75747-560">正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。</span><span class="sxs-lookup"><span data-stu-id="75747-560">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="75747-561">Blazor はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。</span><span class="sxs-lookup"><span data-stu-id="75747-561">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="75747-562">シーケンス番号を使用すると、比較ははるかに高速になります。 Blazor には、開発者がファイルを作成`.razor`するときにシーケンス番号を自動的に処理するコンパイル手順の利点があります。</span><span class="sxs-lookup"><span data-stu-id="75747-562">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="75747-563">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="75747-563">Localization</span></span>

<span data-ttu-id="75747-564">Blazor サーバー側アプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="75747-564">Blazor server-side apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="75747-565">ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。</span><span class="sxs-lookup"><span data-stu-id="75747-565">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="75747-566">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="75747-566">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="75747-567">クッキー</span><span class="sxs-lookup"><span data-stu-id="75747-567">Cookies</span></span>](#cookies)
* [<span data-ttu-id="75747-568">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="75747-568">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="75747-569">使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-569">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="75747-570">クッキー</span><span class="sxs-lookup"><span data-stu-id="75747-570">Cookies</span></span>

<span data-ttu-id="75747-571">ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。</span><span class="sxs-lookup"><span data-stu-id="75747-571">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="75747-572">Cookie は、アプリのホスト`OnGet`ページ (*Pages/host. cshtml. .cs*) のメソッドによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="75747-572">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="75747-573">ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="75747-573">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="75747-574">Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。</span><span class="sxs-lookup"><span data-stu-id="75747-574">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="75747-575">ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。</span><span class="sxs-lookup"><span data-stu-id="75747-575">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="75747-576">したがって、ローカライズカルチャ cookie を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="75747-576">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="75747-577">カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="75747-577">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="75747-578">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="75747-578">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="75747-579">次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="75747-579">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="75747-580">Blazor サーバー側アプリに次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="75747-580">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor server-side app:</span></span>

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

<span data-ttu-id="75747-581">ローカライズはアプリで処理されます。</span><span class="sxs-lookup"><span data-stu-id="75747-581">Localization is handled in the app:</span></span>

1. <span data-ttu-id="75747-582">ブラウザーは、アプリに最初の HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="75747-582">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="75747-583">カルチャは、ローカリゼーションミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="75747-583">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="75747-584">_Host `OnGet`のメソッドは、応答の一部として cookie 内のカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="75747-584">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="75747-585">ブラウザーは WebSocket 接続を開き、対話型の Blazor サーバー側セッションを作成します。</span><span class="sxs-lookup"><span data-stu-id="75747-585">The browser opens a WebSocket connection to create an interactive Blazor server-side session.</span></span>
1. <span data-ttu-id="75747-586">ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="75747-586">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="75747-587">Blazor のサーバー側セッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="75747-587">The Blazor server-side session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="75747-588">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="75747-588">Provide UI to choose the culture</span></span>

<span data-ttu-id="75747-589">ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="75747-589">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="75747-590">このプロセスは、ユーザーがセキュリティで保護されたリソース&mdash;にアクセスしようとしたときに、ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされるという点に似ています。</span><span class="sxs-lookup"><span data-stu-id="75747-590">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="75747-591">アプリは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="75747-591">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="75747-592">コントローラーは、ユーザーが選択したカルチャを cookie に設定し、ユーザーを元の URI にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="75747-592">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="75747-593">Cookie でユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するために、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="75747-593">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="75747-594">アクションの`LocalRedirect`結果を使用して、開いているリダイレクト攻撃を防止します。</span><span class="sxs-lookup"><span data-stu-id="75747-594">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="75747-595">詳細については、「 <xref:security/preventing-open-redirects> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-595">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="75747-596">次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="75747-596">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject IUriHelper UriHelper

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(UIChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(UriHelper.GetAbsoluteUri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        UriHelper.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="75747-597">Blazor アプリで .NET ローカライズシナリオを使用する</span><span class="sxs-lookup"><span data-stu-id="75747-597">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="75747-598">Blazor アプリ内では、次の .NET ローカリゼーションとグローバリゼーションのシナリオを利用できます。</span><span class="sxs-lookup"><span data-stu-id="75747-598">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="75747-599">.NET のリソースシステム</span><span class="sxs-lookup"><span data-stu-id="75747-599">.NET's resources system</span></span>
* <span data-ttu-id="75747-600">カルチャ固有の数値と日付の書式設定</span><span class="sxs-lookup"><span data-stu-id="75747-600">Culture-specific number and date formatting</span></span>

<span data-ttu-id="75747-601">Blazor の`@bind`機能は、ユーザーの現在のカルチャに基づいてグローバリゼーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="75747-601">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="75747-602">詳細については、「[データバインディング](#data-binding)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-602">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="75747-603">現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="75747-603">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="75747-604">`IStringLocalizer<>`は、Blazor アプリで*サポートされて*います。</span><span class="sxs-lookup"><span data-stu-id="75747-604">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="75747-605">`IHtmlLocalizer<>`、 `IViewLocalizer<>`、およびデータ注釈のローカライズは、Blazor アプリではサポートされて**いない**MVC シナリオ ASP.NET Core ます。</span><span class="sxs-lookup"><span data-stu-id="75747-605">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="75747-606">詳細については、「 <xref:fundamentals/localization> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75747-606">For more information, see <xref:fundamentals/localization>.</span></span>
