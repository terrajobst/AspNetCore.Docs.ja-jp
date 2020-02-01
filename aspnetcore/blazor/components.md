---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: d6ba60b20d21636c7f780a80d8fbdb152505a3a3
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928260"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="1ca38-103">ASP.NET Core Razor コンポーネントを作成して使用する</span><span class="sxs-lookup"><span data-stu-id="1ca38-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="1ca38-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1ca38-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1ca38-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1ca38-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1ca38-106">Blazor アプリは*コンポーネント*を使用して構築されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="1ca38-107">コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="1ca38-108">コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="1ca38-109">コンポーネントは柔軟で軽量です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="1ca38-110">入れ子にして再利用したり、プロジェクト間で共有したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="1ca38-111">コンポーネントクラス</span><span class="sxs-lookup"><span data-stu-id="1ca38-111">Component classes</span></span>

<span data-ttu-id="1ca38-112">コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="1ca38-113">Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="1ca38-114">コンポーネントの名前は、大文字で始まる必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="1ca38-115">たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="1ca38-116">コンポーネントの UI は、HTML を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="1ca38-117">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="1ca38-118">アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="1ca38-119">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="1ca38-120">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="1ca38-121">`@code` ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッド、またはその他のコンポーネントロジックを定義するために指定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="1ca38-122">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="1ca38-123">コンポーネントメンバーは、`@`で始まる式を使用してC# 、コンポーネントのレンダリングロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="1ca38-124">たとえば、 C#フィールド名をプレフィックス `@` によって表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="1ca38-125">次の例では、が評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="1ca38-126">`font-style`の CSS プロパティ値に `_headingFontStyle` します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="1ca38-127">`<h1>` 要素の内容に `_headingText` します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="1ca38-128">コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="1ca38-129">Blazor は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="1ca38-130">コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="1ca38-131">Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="1ca38-132">ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="1ca38-133">通常、コンポーネントの名前空間は、アプリのルート名前空間と、アプリ内のコンポーネントの場所 (フォルダー) から派生します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="1ca38-134">アプリのルート名前空間が `BlazorApp`、`Counter` コンポーネントが*Pages*フォルダーに存在する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="1ca38-135">`Counter` コンポーネントの名前空間が `BlazorApp.Pages`。</span><span class="sxs-lookup"><span data-stu-id="1ca38-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="1ca38-136">コンポーネントの完全修飾型名が `BlazorApp.Pages.Counter`ます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="1ca38-137">詳細については、「[コンポーネントのインポート](#import-components)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="1ca38-138">カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を親コンポーネントまたはアプリの *_Imports razor*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="1ca38-139">たとえば、次の名前空間は、アプリのルート名前空間が `BlazorApp`場合*に、コンポーネントフォルダー内*のコンポーネントを使用可能にします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="1ca38-140">コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="1ca38-140">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="1ca38-141">Razor コンポーネントは Razor Pages と MVC アプリに統合できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-141">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="1ca38-142">ページまたはビューが表示されると、コンポーネントを同時に prerendered することができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-142">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="1ca38-143">Razor コンポーネントをホストする Razor Pages または MVC アプリを準備するには、<xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps> の記事の「 *razor コンポーネントを Razor Pages および mvc アプリに統合*する」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-143">To prepare a Razor Pages or MVC app to host Razor components, follow the guidance in the *Integrate Razor components into Razor Pages and MVC apps* section of the <xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps> article.</span></span>

<span data-ttu-id="1ca38-144">カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間をページ/ビューまたは *_ViewImports*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-144">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1ca38-145">たとえば、行が次のように表示されているとします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-145">In the following example:</span></span>

* <span data-ttu-id="1ca38-146">`MyAppNamespace` をアプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-146">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="1ca38-147">*コンポーネントという名前*のフォルダーを使用してコンポーネントを保持していない場合は、コンポーネントが存在するフォルダーに `Components` を変更します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-147">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```csharp
@using MyAppNamespace.Components
```

<span data-ttu-id="1ca38-148">*_ViewImports*のファイルは、Razor Pages アプリの*Pages*フォルダーまたは MVC アプリの*Views*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-148">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="1ca38-149">ページまたはビューからコンポーネントを表示するには、`Component` タグヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-149">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="1ca38-150">パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-150">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="1ca38-151">たとえば、`IncrementAmount` の型は `int`であるため、`IncrementAmount` の値を指定できます。これは、JSON シリアライザーによってサポートされるプリミティブ型です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-151">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="1ca38-152">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-152">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="1ca38-153">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-153">Is prerendered into the page.</span></span>
* <span data-ttu-id="1ca38-154">は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-154">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="1ca38-155">説明</span><span class="sxs-lookup"><span data-stu-id="1ca38-155">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="1ca38-156">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-156">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1ca38-157">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-157">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="1ca38-158">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-158">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1ca38-159">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-159">Output from the component isn't included.</span></span> <span data-ttu-id="1ca38-160">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-160">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="1ca38-161">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-161">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1ca38-162">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-162">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1ca38-163">コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-163">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="1ca38-164">コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-164">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1ca38-165">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-165">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="1ca38-166">コンポーネントのレンダリング方法、コンポーネントの状態、および `Component` タグヘルパーの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-166">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

## <a name="tag-helpers-arent-used-in-components"></a><span data-ttu-id="1ca38-167">タグヘルパーはコンポーネントで使用されていません</span><span class="sxs-lookup"><span data-stu-id="1ca38-167">Tag Helpers aren't used in components</span></span>

<span data-ttu-id="1ca38-168">[タグヘルパー](xref:mvc/views/tag-helpers/intro)は razor コンポーネント (*razor*ファイル) ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-168">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="1ca38-169">Blazor でタグヘルパーのような機能を提供するには、タグヘルパーと同じ機能を持つコンポーネントを作成し、代わりにそのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-169">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="1ca38-170">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="1ca38-170">Use components</span></span>

<span data-ttu-id="1ca38-171">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-171">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="1ca38-172">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-172">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="1ca38-173">属性のバインドでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-173">Attribute binding is case sensitive.</span></span> <span data-ttu-id="1ca38-174">たとえば、`@bind` が有効で、`@Bind` が無効です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-174">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="1ca38-175">*Index. razor*の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-175">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="1ca38-176">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-176">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="1ca38-177">コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-177">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="1ca38-178">コンポーネントの名前空間に `@using` ステートメントを追加すると、コンポーネントが使用可能になり、警告が削除されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-178">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="1ca38-179">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="1ca38-179">Component parameters</span></span>

<span data-ttu-id="1ca38-180">コンポーネントには、コンポーネントクラスのパブリックプロパティを使用して定義されるコンポーネント*パラメーター*を含めることができます。これは、`[Parameter]` 属性を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-180">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="1ca38-181">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="1ca38-181">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="1ca38-182">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-182">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="1ca38-183">サンプルアプリの次の例では、`ParentComponent` によって `ChildComponent`の `Title` プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-183">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="1ca38-184">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-184">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a><span data-ttu-id="1ca38-185">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="1ca38-185">Child content</span></span>

<span data-ttu-id="1ca38-186">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-186">Components can set the content of another component.</span></span> <span data-ttu-id="1ca38-187">割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-187">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="1ca38-188">次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す `RenderFragment`を表す `ChildContent` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-188">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="1ca38-189">`ChildContent` の値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップに配置されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-189">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="1ca38-190">`ChildContent` の値は、親コンポーネントから受信され、ブートストラップパネルの `panel-body`内に表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-190">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="1ca38-191">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-191">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="1ca38-192">`RenderFragment` コンテンツを受け取るプロパティには、規約によって `ChildContent` 名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-192">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="1ca38-193">サンプルアプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` を表示するためのコンテンツを提供できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-193">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="1ca38-194">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-194">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="1ca38-195">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="1ca38-195">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="1ca38-196">コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-196">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="1ca38-197">Splatted Razor ディレクティブ[`@attributes`](xref:mvc/views/razor#attributes)を使用してコンポーネントがレンダリングされるときに、ディクショナリ内で追加の属性をキャプチャし、要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-197">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="1ca38-198">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-198">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="1ca38-199">たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-199">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="1ca38-200">次の例では、最初の `<input>` 要素 (`id="useIndividualParams"`) は個々のコンポーネントパラメーターを使用し、2番目の `<input>` 要素 (`id="useAttributesDict"`) は属性スプラッティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-200">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="1ca38-201">パラメーターの型は、文字列キーを使用して `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-201">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="1ca38-202">このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-202">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="1ca38-203">両方の方法を使用して表示される `<input>` 要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-203">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="1ca38-204">任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true`に設定して、`[Parameter]` 属性を使用してコンポーネントパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-204">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="1ca38-205">`[Parameter]` の `CaptureUnmatchedValues` プロパティを使用すると、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-205">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="1ca38-206">コンポーネントは、`CaptureUnmatchedValues`で1つのパラメーターのみを定義できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-206">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="1ca38-207">`CaptureUnmatchedValues` で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-207">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="1ca38-208">このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` もオプションです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-208">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="1ca38-209">要素属性の位置を基準とした `@attributes` の位置が重要です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-209">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="1ca38-210">要素に対して `@attributes` が splatted されると、属性は右から左 (最後から順) に処理されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-210">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="1ca38-211">`Child` コンポーネントを使用するコンポーネントの次の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-211">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="1ca38-212">*Parentcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-212">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="1ca38-213">*Childcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-213">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="1ca38-214">`Child` コンポーネントの `extra` 属性が `@attributes`の右側に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-214">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="1ca38-215">属性が右から左に処理されるため、`Parent` コンポーネントのレンダリングされる `<div>` には `extra="5"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-215">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="1ca38-216">次の例では、`Child` コンポーネントの `<div>`で `extra` と `@attributes` の順序が逆になっています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-216">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="1ca38-217">*Parentcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="1ca38-218">*Childcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-218">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="1ca38-219">`Parent` コンポーネントに表示される `<div>` には、追加の属性を通じて渡された `extra="10"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-219">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="1ca38-220">データ バインディング</span><span class="sxs-lookup"><span data-stu-id="1ca38-220">Data binding</span></span>

<span data-ttu-id="1ca38-221">コンポーネントと DOM 要素の両方に対するデータバインディングは、 [`@bind`](xref:mvc/views/razor#bind)属性を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-221">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="1ca38-222">次の例では、`CurrentValue` プロパティをテキストボックスの値にバインドします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-222">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1ca38-223">テキストボックスがフォーカスを失うと、プロパティの値が更新されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-223">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="1ca38-224">テキストボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-224">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="1ca38-225">イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、*通常*、イベントハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-225">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="1ca38-226">`CurrentValue` プロパティ (`<input @bind="CurrentValue" />`) で `@bind` を使用することは、基本的に次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-226">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1ca38-227">コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-227">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="1ca38-228">ユーザーがテキストボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-228">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="1ca38-229">実際には、型変換が実行されるケースが `@bind` によって処理されるため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-229">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="1ca38-230">原則として、`@bind` は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-230">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="1ca38-231">`@bind` 構文を使用した `onchange` イベントの処理に加えて、`event` パラメーター ([`@bind-value:event`](xref:mvc/views/razor#bind)) を使用して[`@bind-value`](xref:mvc/views/razor#bind)属性を指定することで、プロパティまたはフィールドを他のイベントを使用してバインドできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-231">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="1ca38-232">次の例では、`oninput` イベントの `CurrentValue` プロパティをバインドします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-232">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1ca38-233">要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキストボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-233">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="1ca38-234">前の例の `@bind-value` は、次のようにバインドします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-234">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="1ca38-235">要素の `value` 属性に対して指定された式 (`CurrentValue`)。</span><span class="sxs-lookup"><span data-stu-id="1ca38-235">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="1ca38-236">`@bind-value:event`によって指定されたイベントへの変更イベントデリゲート。</span><span class="sxs-lookup"><span data-stu-id="1ca38-236">A change event delegate to the event specified by `@bind-value:event`.</span></span>

<span data-ttu-id="1ca38-237">**解析不可能値**</span><span class="sxs-lookup"><span data-stu-id="1ca38-237">**Unparsable values**</span></span>

<span data-ttu-id="1ca38-238">データバインド要素に解析できない値を指定すると、バインドイベントがトリガーされたときに、解析されていない値が自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-238">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="1ca38-239">このような場合は、まず、次のことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-239">Consider the following scenario:</span></span>

* <span data-ttu-id="1ca38-240">`<input>` 要素は、初期値 `123`を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-240">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="1ca38-241">ユーザーは、要素の値をページの `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-241">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="1ca38-242">前のシナリオでは、要素の値が `123`に戻されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-242">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="1ca38-243">`123`の元の値を優先して `123.45` 値が拒否された場合、ユーザーはその値が受け入れられていないことを認識します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-243">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="1ca38-244">既定では、バインドは要素の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) に適用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-244">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="1ca38-245">別のイベントを設定するには、`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-245">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="1ca38-246">`oninput` イベント (`@bind-value:event="oninput"`) の場合、解析できない値を導入するキーストロークの後に再設定が発生します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-246">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="1ca38-247">`int`バインドされた型の `oninput` イベントを対象とする場合、ユーザーは `.` 文字を入力できません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-247">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="1ca38-248">`.` 文字はすぐに削除されるので、ユーザーは、整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-248">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="1ca38-249">`oninput` イベントの値を元に戻すことは、ユーザーが解析できない `<input>` 値のクリアを許可する必要がある場合など、理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-249">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="1ca38-250">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-250">Alternatives include:</span></span>

* <span data-ttu-id="1ca38-251">`oninput` イベントは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-251">Don't use the `oninput` event.</span></span> <span data-ttu-id="1ca38-252">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-252">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="1ca38-253">`int?` や `string`などの null 許容型にバインドし、無効なエントリを処理するカスタムロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-253">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="1ca38-254">`InputNumber` や `InputDate`などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-254">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="1ca38-255">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-255">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="1ca38-256">フォーム検証コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1ca38-256">Form validation components:</span></span>
  * <span data-ttu-id="1ca38-257">ユーザーが無効な入力を提供し、関連付けられた `EditContext`で検証エラーを受信することを許可します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-257">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="1ca38-258">追加の web フォームデータを入力するユーザーに干渉することなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-258">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="1ca38-259">**グローバリゼーション**</span><span class="sxs-lookup"><span data-stu-id="1ca38-259">**Globalization**</span></span>

<span data-ttu-id="1ca38-260">`@bind` 値は、現在のカルチャの規則を使用して表示および解析するように書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-260">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="1ca38-261">現在のカルチャには、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-261">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="1ca38-262">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-262">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="1ca38-263">上記のフィールド型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-263">The preceding field types:</span></span>

* <span data-ttu-id="1ca38-264">は、適切なブラウザーベースの書式規則を使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-264">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="1ca38-265">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-265">Can't contain free-form text.</span></span>
* <span data-ttu-id="1ca38-266">ブラウザーの実装に基づいてユーザーの操作特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-266">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="1ca38-267">次のフィールド型には特定の書式要件があり、Blazor では現在サポートされていません。これは、すべての主要なブラウザーでサポートされていないためです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-267">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="1ca38-268">`@bind` では、`@bind:culture` パラメーターを使用して、値の解析および書式設定のための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-268">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="1ca38-269">`date` および `number` のフィールドの種類を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-269">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="1ca38-270">`date` と `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-270">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="1ca38-271">ユーザーのカルチャを設定する方法については、「[ローカリゼーション](#localization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-271">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="1ca38-272">**書式指定文字列**</span><span class="sxs-lookup"><span data-stu-id="1ca38-272">**Format strings**</span></span>

<span data-ttu-id="1ca38-273">データバインディングは、 [`@bind:format`](xref:mvc/views/razor#bind)を使用して <xref:System.DateTime> 書式指定文字列で動作します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-273">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="1ca38-274">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-274">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="1ca38-275">前のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text`に設定されています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-275">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="1ca38-276">`@bind:format` は、次の .NET 型のバインドに対してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-276">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="1ca38-277"><xref:System.DateTime?displayProperty=fullName> ですか。</span><span class="sxs-lookup"><span data-stu-id="1ca38-277"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="1ca38-278"><xref:System.DateTimeOffset?displayProperty=fullName> ですか。</span><span class="sxs-lookup"><span data-stu-id="1ca38-278"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="1ca38-279">`@bind:format` 属性は、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-279">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="1ca38-280">この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-280">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="1ca38-281">Blazor には日付を書式設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-281">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="1ca38-282">推奨事項では、`date` フィールドの種類で形式が指定されている場合、バインドの `yyyy-MM-dd` 日付形式のみを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-282">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

<span data-ttu-id="1ca38-283">**コンポーネントのパラメーター**</span><span class="sxs-lookup"><span data-stu-id="1ca38-283">**Component parameters**</span></span>

<span data-ttu-id="1ca38-284">バインディングはコンポーネントパラメーターを認識しますが、`@bind-{property}` はコンポーネント間でプロパティ値をバインドできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-284">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="1ca38-285">次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネントパラメーターと `YearChanged` コールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-285">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="1ca38-286">`EventCallback<T>` については、「 [Eventcallback](#eventcallback) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-286">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="1ca38-287">次の親コンポーネントは `ChildComponent` を使用し、親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-287">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```razor
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

<span data-ttu-id="1ca38-288">`ParentComponent` を読み込むと、次のマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-288">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="1ca38-289">`ParentComponent`のボタンを選択して `ParentYear` プロパティの値が変更された場合は、`ChildComponent` の `Year` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-289">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="1ca38-290">`Year` の新しい値は、`ParentComponent` が再度実行されたときに UI に表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-290">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="1ca38-291">`Year` パラメーターは、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-291">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="1ca38-292">慣例により、`<ChildComponent @bind-Year="ParentYear" />` は基本的には書き込みと同じです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-292">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="1ca38-293">一般に、プロパティは、`@bind-property:event` 属性を使用して、対応するイベントハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-293">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="1ca38-294">たとえば、プロパティ `MyProp` は、次の2つの属性を使用して `MyEventHandler` にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-294">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

<span data-ttu-id="1ca38-295">**オプションボタン**</span><span class="sxs-lookup"><span data-stu-id="1ca38-295">**Radio buttons**</span></span>

<span data-ttu-id="1ca38-296">フォームのオプションボタンへのバインドの詳細については、「<xref:blazor/forms-validation#work-with-radio-buttons>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-296">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>

## <a name="event-handling"></a><span data-ttu-id="1ca38-297">イベント処理</span><span class="sxs-lookup"><span data-stu-id="1ca38-297">Event handling</span></span>

<span data-ttu-id="1ca38-298">Razor コンポーネントは、イベント処理機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-298">Razor components provide event handling features.</span></span> <span data-ttu-id="1ca38-299">`on{EVENT}` という名前の HTML 要素属性 (`onclick`、`onsubmit`など) とデリゲート型の値がある場合、Razor コンポーネントはその属性の値をイベントハンドラーとして扱います。</span><span class="sxs-lookup"><span data-stu-id="1ca38-299">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="1ca38-300">属性の名前は常に[`@on{EVENT}`](xref:mvc/views/razor#onevent)書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-300">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="1ca38-301">次のコードは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-301">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
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

<span data-ttu-id="1ca38-302">次のコードは、UI でチェックボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-302">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="1ca38-303">イベントハンドラーを非同期にして、<xref:System.Threading.Tasks.Task>を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-303">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="1ca38-304">[Statehaschanged](xref:blazor/lifecycle#state-changes)を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-304">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="1ca38-305">例外は、発生するとログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-305">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="1ca38-306">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-306">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
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

### <a name="event-argument-types"></a><span data-ttu-id="1ca38-307">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="1ca38-307">Event argument types</span></span>

<span data-ttu-id="1ca38-308">イベントによっては、イベント引数の型が許可されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-308">For some events, event argument types are permitted.</span></span> <span data-ttu-id="1ca38-309">これらのイベントの種類のいずれかにアクセスする必要がない場合は、メソッドの呼び出しで必要とされません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-309">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="1ca38-310">次の表に、サポートされている `EventArgs` を示します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-310">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="1ca38-311">Event</span><span class="sxs-lookup"><span data-stu-id="1ca38-311">Event</span></span>            | <span data-ttu-id="1ca38-312">&lt;クラス&gt; のすべてのオブジェクト</span><span class="sxs-lookup"><span data-stu-id="1ca38-312">Class</span></span>                | <span data-ttu-id="1ca38-313">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="1ca38-313">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="1ca38-314">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="1ca38-314">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="1ca38-315">`oncut`では、 `oncopy`では、 `onpaste`</span><span class="sxs-lookup"><span data-stu-id="1ca38-315">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="1ca38-316">抗力</span><span class="sxs-lookup"><span data-stu-id="1ca38-316">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="1ca38-317">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="1ca38-317">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="1ca38-318">`DataTransfer` および `DataTransferItem` ドラッグした項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-318">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="1ca38-319">エラー</span><span class="sxs-lookup"><span data-stu-id="1ca38-319">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="1ca38-320">Event</span><span class="sxs-lookup"><span data-stu-id="1ca38-320">Event</span></span>            | `EventArgs`          | <span data-ttu-id="1ca38-321">*全般*</span><span class="sxs-lookup"><span data-stu-id="1ca38-321">*General*</span></span><br><span data-ttu-id="1ca38-322">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="1ca38-322">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="1ca38-323">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="1ca38-323">*Clipboard*</span></span><br><span data-ttu-id="1ca38-324">`onbeforecut`では、 `onbeforecopy`では、 `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="1ca38-324">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="1ca38-325">*入力*</span><span class="sxs-lookup"><span data-stu-id="1ca38-325">*Input*</span></span><br><span data-ttu-id="1ca38-326">`oninvalid`、 `onreset`、 `onselect`、 `onselectionchange`、 `onselectstart`、 `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="1ca38-326">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="1ca38-327">*メディア*</span><span class="sxs-lookup"><span data-stu-id="1ca38-327">*Media*</span></span><br><span data-ttu-id="1ca38-328">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`の `onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、および `onsuspend`</span><span class="sxs-lookup"><span data-stu-id="1ca38-328">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="1ca38-329">フォーカス</span><span class="sxs-lookup"><span data-stu-id="1ca38-329">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="1ca38-330">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="1ca38-330">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="1ca38-331">には `relatedTarget`のサポートは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-331">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="1ca38-332">[入力]</span><span class="sxs-lookup"><span data-stu-id="1ca38-332">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="1ca38-333">`onchange`、`oninput`</span><span class="sxs-lookup"><span data-stu-id="1ca38-333">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="1ca38-334">キーボード</span><span class="sxs-lookup"><span data-stu-id="1ca38-334">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="1ca38-335">`onkeydown`では、 `onkeypress`では、 `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="1ca38-335">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="1ca38-336">マウス</span><span class="sxs-lookup"><span data-stu-id="1ca38-336">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="1ca38-337">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="1ca38-337">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="1ca38-338">マウスポインター</span><span class="sxs-lookup"><span data-stu-id="1ca38-338">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="1ca38-339">`onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="1ca38-339">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="1ca38-340">マウスホイール</span><span class="sxs-lookup"><span data-stu-id="1ca38-340">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="1ca38-341">`onwheel`、`onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="1ca38-341">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="1ca38-342">中</span><span class="sxs-lookup"><span data-stu-id="1ca38-342">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="1ca38-343">`onabort`、 `onload`、 `onloadend`、 `onloadstart`、 `onprogress`、 `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="1ca38-343">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="1ca38-344">タッチ</span><span class="sxs-lookup"><span data-stu-id="1ca38-344">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="1ca38-345">`ontouchstart`、 `ontouchend`、 `ontouchmove`、 `ontouchenter`、 `ontouchleave`、 `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="1ca38-345">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="1ca38-346">`TouchPoint` は、タッチを区別するデバイス上の1つの連絡先ポイントを表します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-346">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="1ca38-347">前の表に示したイベントのプロパティとイベント処理動作の詳細については、「[参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 分岐)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-347">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="1ca38-348">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="1ca38-348">Lambda expressions</span></span>

<span data-ttu-id="1ca38-349">ラムダ式を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-349">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="1ca38-350">多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-350">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="1ca38-351">次の例では、3つのボタンを作成します。各ボタンは、UI で選択されたときに、イベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-351">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="1ca38-352">ラムダ式では、`for` ループでループ変数 (`i`) を**直接使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="1ca38-352">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="1ca38-353">それ以外の場合、すべてのラムダ式で同じ変数が使用され、`i`の値はすべてのラムダで同じになります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-353">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="1ca38-354">常にローカル変数 (前の例では`buttonNumber`) で値をキャプチャし、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-354">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="1ca38-355">EventCallback</span><span class="sxs-lookup"><span data-stu-id="1ca38-355">EventCallback</span></span>

<span data-ttu-id="1ca38-356">入れ子になったコンポーネントの一般的なシナリオとして、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行することが望まれます。たとえば、子で `onclick` イベントが発生した場合などです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-356">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="1ca38-357">コンポーネント間でイベントを公開するには、`EventCallback`を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-357">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="1ca38-358">親コンポーネントは、コールバックメソッドを子コンポーネントの `EventCallback`に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-358">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="1ca38-359">サンプルアプリ (*Components/ChildComponent. razor*) の `ChildComponent` は、ボタンの `onclick` ハンドラーが、サンプルの `ParentComponent`から `EventCallback` デリゲートを受け取るように設定されている方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-359">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="1ca38-360">`EventCallback` は `MouseEventArgs`で入力されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-360">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="1ca38-361">`ParentComponent` によって、子の `EventCallback<T>` (`OnClick`) が `ShowMessage` メソッドに設定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-361">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClick`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="1ca38-362">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-362">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="1ca38-363">`ChildComponent`でボタンが選択されている場合:</span><span class="sxs-lookup"><span data-stu-id="1ca38-363">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="1ca38-364">`ParentComponent`の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-364">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="1ca38-365">`_messageText` が更新され、`ParentComponent`に表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-365">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="1ca38-366">このコールバックのメソッド (`ShowMessage`) では、 [Statehaschanged](xref:blazor/lifecycle#state-changes)の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-366">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="1ca38-367">`StateHasChanged` は、子イベントと同様に、子の内部で実行されるイベントハンドラーでコンポーネントの実行がトリガーされるのと同様に、`ParentComponent`をレンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-367">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="1ca38-368">`EventCallback` および `EventCallback<T>` 非同期デリゲートを許可します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-368">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="1ca38-369">`EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-369">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="1ca38-370">`EventCallback` は弱く型指定され、任意の引数型を使用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-370">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="1ca38-371">`InvokeAsync` で `EventCallback` または `EventCallback<T>` を呼び出し、<xref:System.Threading.Tasks.Task>を待機します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-371">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="1ca38-372">イベント処理とバインドコンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-372">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="1ca38-373">厳密に型指定された `EventCallback<T>` `EventCallback`を優先します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-373">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="1ca38-374">`EventCallback<T>` は、コンポーネントのユーザーに対してより適切なエラーフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-374">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="1ca38-375">他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-375">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="1ca38-376">コールバックに値が渡されない場合は、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-376">Use `EventCallback` when there's no value passed to the callback.</span></span>

### <a name="prevent-default-actions"></a><span data-ttu-id="1ca38-377">既定のアクションを禁止する</span><span class="sxs-lookup"><span data-stu-id="1ca38-377">Prevent default actions</span></span>

<span data-ttu-id="1ca38-378">イベントの既定のアクションを回避するには、 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-378">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="1ca38-379">入力デバイスでキーが選択され、要素のフォーカスがテキストボックスに表示されている場合は、通常、ブラウザーによってテキストボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-379">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="1ca38-380">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作を回避できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-380">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="1ca38-381">カウンターがインクリメントされ、 **+** キーが `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-381">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="1ca38-382">値を指定せずに `@on{EVENT}:preventDefault` 属性を指定することは `@on{EVENT}:preventDefault="true"`と同じです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-382">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="1ca38-383">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-383">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="1ca38-384">次の例では、`_shouldPreventDefault` は `true` または `false`のいずれかに設定された `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-384">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="1ca38-385">既定のアクションを防ぐために、イベントハンドラーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-385">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="1ca38-386">イベントハンドラーを使用したり、既定のアクションシナリオを回避したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-386">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="1ca38-387">イベントの伝達の停止</span><span class="sxs-lookup"><span data-stu-id="1ca38-387">Stop event propagation</span></span>

<span data-ttu-id="1ca38-388">イベントの伝達を停止するには、 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-388">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="1ca38-389">次の例では、チェックボックスをオンにすると、2番目の子 `<div>` からのクリックイベントが親 `<div>`に反映されなくなります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-389">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

## <a name="chained-bind"></a><span data-ttu-id="1ca38-390">チェーンバインド</span><span class="sxs-lookup"><span data-stu-id="1ca38-390">Chained bind</span></span>

<span data-ttu-id="1ca38-391">一般的なシナリオでは、データバインドパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-391">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="1ca38-392">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーンバインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-392">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="1ca38-393">チェーンバインドは、ページの要素で `@bind` 構文を使用して実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-393">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="1ca38-394">イベントハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-394">The event handler and value must be specified separately.</span></span> <span data-ttu-id="1ca38-395">ただし、親コンポーネントでは、`@bind` 構文をコンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-395">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="1ca38-396">次の `PasswordField` コンポーネント (*Passwordfield*):</span><span class="sxs-lookup"><span data-stu-id="1ca38-396">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="1ca38-397">`Password` プロパティに `<input>` 要素の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-397">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="1ca38-398">[Eventcallback](#eventcallback)を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-398">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="1ca38-399">`PasswordField` コンポーネントが別のコンポーネントで使用されています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-399">The `PasswordField` component is used in another component:</span></span>

```razor
<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="1ca38-400">前の例で、パスワードの確認またはトラップエラーを実行するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-400">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="1ca38-401">`Password` のバッキングフィールドを作成します (次のコード例では`_password`)。</span><span class="sxs-lookup"><span data-stu-id="1ca38-401">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="1ca38-402">`Password` setter で、チェックまたはトラップのエラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-402">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="1ca38-403">次の例では、パスワードの値にスペースが使用されている場合に、ユーザーにすぐにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-403">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="1ca38-404">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="1ca38-404">Capture references to components</span></span>

<span data-ttu-id="1ca38-405">コンポーネント参照を使用すると、`Show` や `Reset`などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-405">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="1ca38-406">コンポーネント参照をキャプチャするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-406">To capture a component reference:</span></span>

* <span data-ttu-id="1ca38-407">子コンポーネントに[`@ref`](xref:mvc/views/razor#ref)属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-407">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="1ca38-408">子コンポーネントと同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-408">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="1ca38-409">コンポーネントがレンダリングされると、[`_loginDialog`] フィールドに `MyLoginDialog` 子コンポーネントインスタンスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-409">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="1ca38-410">その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-410">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1ca38-411">`_loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-411">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="1ca38-412">この時点までは、参照するものはありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-412">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="1ca38-413">コンポーネント参照のレンダリングが完了した後にコンポーネント参照を操作するに[は、OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](xref:blazor/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-413">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="1ca38-414">コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-414">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="1ca38-415">コンポーネント参照は、.NET コードでのみ使用される&mdash;JavaScript コードに渡されません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-415">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="1ca38-416">子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-416">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="1ca38-417">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-417">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="1ca38-418">通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-418">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="1ca38-419">状態を更新するためにコンポーネントメソッドを外部に呼び出す</span><span class="sxs-lookup"><span data-stu-id="1ca38-419">Invoke component methods externally to update state</span></span>

<span data-ttu-id="1ca38-420">Blazor は、1つの論理スレッドの実行を強制するために `SynchronizationContext` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-420">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="1ca38-421">Blazor によって発生するコンポーネントの[ライフサイクルメソッド](xref:blazor/lifecycle)とイベントコールバックは、この `SynchronizationContext`で実行されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-421">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="1ca38-422">タイマーやその他の通知など、外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。このメソッドは、Blazor の `SynchronizationContext`にディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-422">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="1ca38-423">たとえば、更新された状態のすべてのリッスンコンポーネントに通知できる通知*サービス*を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-423">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="1ca38-424">コンポーネントを更新するための `NotifierService` の使用法:</span><span class="sxs-lookup"><span data-stu-id="1ca38-424">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="1ca38-425">前の例では、`NotifierService` によって、コンポーネントの `OnNotify` メソッドが Blazor の `SynchronizationContext`の外部で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-425">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="1ca38-426">`InvokeAsync` は、正しいコンテキストに切り替え、レンダリングをキューに移動するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-426">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="1ca38-427">\@キーを使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="1ca38-427">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="1ca38-428">要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-428">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="1ca38-429">通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-429">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="1ca38-430">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-430">Consider the following example:</span></span>

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

<span data-ttu-id="1ca38-431">`People` コレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-431">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="1ca38-432">コンポーネントのれ時に、`<DetailsEditor>` コンポーネントが異なる `Details` パラメーター値を受け取るように変更されることがあります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-432">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="1ca38-433">これにより、予想よりも複雑な再リリースが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-433">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="1ca38-434">場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-434">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="1ca38-435">マッピングプロセスは、`@key` ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-435">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="1ca38-436">`@key` によって、キーの値に基づいて要素またはコンポーネントの保持が比較アルゴリズムによって保証されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-436">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="1ca38-437">`People` コレクションが変更されると、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンスの間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-437">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="1ca38-438">`Person` が `People` の一覧から削除された場合は、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-438">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="1ca38-439">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-439">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1ca38-440">リスト内のある位置に `Person` が挿入されると、その対応する位置に1つの新しい `<DetailsEditor>` インスタンスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-440">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="1ca38-441">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-441">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1ca38-442">`Person` エントリが再順序付けされている場合は、対応する `<DetailsEditor>` インスタンスが UI に保持され、並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-442">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="1ca38-443">場合によっては、`@key` を使用すると、回避の複雑さが最小限に抑えられるため、フォーカス位置など、DOM のステートフルな部分に対する潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-443">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1ca38-444">キーは、各コンテナー要素またはコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-444">Keys are local to each container element or component.</span></span> <span data-ttu-id="1ca38-445">キーがドキュメント全体でグローバルに比較されることはありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-445">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="1ca38-446">\@キーを使用する場合</span><span class="sxs-lookup"><span data-stu-id="1ca38-446">When to use \@key</span></span>

<span data-ttu-id="1ca38-447">通常は、リストがレンダリングされるたびに (たとえば、`@foreach` ブロックで) `@key` を使用し、`@key`を定義するための適切な値が存在することを意味します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-447">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="1ca38-448">また、`@key` を使用すると、オブジェクトが変更されたときに、Blazor が要素またはコンポーネントのサブツリーを保持しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-448">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="1ca38-449">`@currentPerson` が変更された場合、`@key` attribute ディレクティブは、Blazor が `<div>` とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築することを強制します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-449">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="1ca38-450">これは、`@currentPerson` 変更時に UI の状態が保持されないことを保証する必要がある場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-450">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="1ca38-451">\@キーを使用しない場合</span><span class="sxs-lookup"><span data-stu-id="1ca38-451">When not to use \@key</span></span>

<span data-ttu-id="1ca38-452">`@key`を使用すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-452">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="1ca38-453">パフォーマンスコストは大きくありませんが、要素またはコンポーネントの保存規則を制御することによってアプリにメリットがある場合にのみ `@key` を指定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-453">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="1ca38-454">`@key` が使用されていない場合でも、Blazor は子要素とコンポーネントインスタンスを可能な限り保持します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-454">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="1ca38-455">`@key` を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-455">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="1ca38-456">\@キーに使用する値</span><span class="sxs-lookup"><span data-stu-id="1ca38-456">What values to use for \@key</span></span>

<span data-ttu-id="1ca38-457">一般に、`@key`には、次のいずれかの値を指定するのが理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-457">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="1ca38-458">モデルオブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。</span><span class="sxs-lookup"><span data-stu-id="1ca38-458">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="1ca38-459">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-459">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="1ca38-460">一意の識別子 (たとえば、`int`型、`string`型、`Guid`型の主キー値)。</span><span class="sxs-lookup"><span data-stu-id="1ca38-460">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="1ca38-461">`@key` に使用される値が競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-461">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="1ca38-462">競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-462">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="1ca38-463">個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-463">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="1ca38-464">ルーティング</span><span class="sxs-lookup"><span data-stu-id="1ca38-464">Routing</span></span>

<span data-ttu-id="1ca38-465">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-465">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="1ca38-466">`@page` ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-466">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="1ca38-467">実行時に、ルーターは `RouteAttribute` を持つコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-467">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="1ca38-468">コンポーネントには、複数のルートテンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-468">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="1ca38-469">次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute`に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-469">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="1ca38-470">*Pages/BlazorRoute*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-470">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="1ca38-471">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="1ca38-471">Route parameters</span></span>

<span data-ttu-id="1ca38-472">コンポーネントは、`@page` ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-472">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="1ca38-473">ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-473">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="1ca38-474">*Pages/RouteParameter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-474">*Pages/RouteParameter.razor*:</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="1ca38-475">省略可能なパラメーターはサポートされていないため、上記の例では2つの `@page` ディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-475">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="1ca38-476">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-476">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="1ca38-477">2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-477">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="1ca38-478">複数のフォルダー境界をまたいでパスをキャプチャする*キャッチオール*パラメーター構文 (`*`/`**`) は、razor コンポーネント (*razor*) ではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="1ca38-478">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="1ca38-479">部分クラスのサポート</span><span class="sxs-lookup"><span data-stu-id="1ca38-479">Partial class support</span></span>

<span data-ttu-id="1ca38-480">Razor コンポーネントは部分クラスとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-480">Razor components are generated as partial classes.</span></span> <span data-ttu-id="1ca38-481">Razor コンポーネントは、次のいずれかの方法を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-481">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="1ca38-482">C#コードは、1つのファイル内に HTML マークアップと Razor コードを含む[`@code`](xref:mvc/views/razor#code)ブロックで定義されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-482">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="1ca38-483">Blazor テンプレートでは、この方法を使用して Razor コンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-483">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="1ca38-484">C#コードは、部分クラスとして定義されている分離コードファイルに配置されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-484">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="1ca38-485">次の例は、Blazor テンプレートから生成されたアプリで `@code` ブロックを持つ既定の `Counter` コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-485">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="1ca38-486">HTML マークアップ、Razor コード、 C#およびコードは、同じファイル内にあります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-486">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="1ca38-487">*Counter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-487">*Counter.razor*:</span></span>

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

<span data-ttu-id="1ca38-488">`Counter` コンポーネントは、部分クラスの分離コードファイルを使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-488">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="1ca38-489">*Counter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-489">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="1ca38-490">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-490">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="1ca38-491">必要に応じて、部分クラスファイルに必要な名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-491">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="1ca38-492">Razor コンポーネントで使用される一般的な名前空間は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-492">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="1ca38-493">基底クラスの指定</span><span class="sxs-lookup"><span data-stu-id="1ca38-493">Specify a base class</span></span>

<span data-ttu-id="1ca38-494">[`@inherits`](xref:mvc/views/razor#inherits)ディレクティブは、コンポーネントの基底クラスを指定するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-494">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="1ca38-495">次の例は、コンポーネントが基本クラス `BlazorRocksBase`を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-495">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="1ca38-496">基底クラスは `ComponentBase`から派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-496">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="1ca38-497">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-497">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="1ca38-498">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="1ca38-498">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="1ca38-499">属性を指定する</span><span class="sxs-lookup"><span data-stu-id="1ca38-499">Specify an attribute</span></span>

<span data-ttu-id="1ca38-500">属性は、 [`@attribute`](xref:mvc/views/razor#attribute)ディレクティブを使用して Razor コンポーネントで指定できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-500">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="1ca38-501">次の例では、`[Authorize]` 属性を component クラスに適用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-501">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="1ca38-502">コンポーネントのインポート</span><span class="sxs-lookup"><span data-stu-id="1ca38-502">Import components</span></span>

<span data-ttu-id="1ca38-503">Razor で作成されるコンポーネントの名前空間は、(優先順位に従って) に基づきます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-503">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="1ca38-504">Razor ファイル (*razor*) マークアップ (`@namespace BlazorSample.MyNamespace`) の[`@namespace`](xref:mvc/views/razor#namespace)指定。</span><span class="sxs-lookup"><span data-stu-id="1ca38-504">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="1ca38-505">プロジェクトファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。</span><span class="sxs-lookup"><span data-stu-id="1ca38-505">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="1ca38-506">プロジェクトファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクトルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="1ca38-506">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="1ca38-507">たとえば、フレームワークは *{PROJECT ROOT}/* *BlazorSample (* ) を名前空間 `BlazorSample.Pages`に解決します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-507">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="1ca38-508">コンポーネントはC# 、名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="1ca38-508">Components follow C# name binding rules.</span></span> <span data-ttu-id="1ca38-509">この例の `Index` コンポーネントの場合、スコープ内のコンポーネントはすべてコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-509">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="1ca38-510">同じフォルダー内の*ページ*。</span><span class="sxs-lookup"><span data-stu-id="1ca38-510">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="1ca38-511">別の名前空間を明示的に指定していない、プロジェクトのルート内のコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="1ca38-511">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="1ca38-512">別の名前空間で定義されているコンポーネントは、Razor の[`@using`](xref:mvc/views/razor#using)ディレクティブを使用してスコープ内に置かれます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-512">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="1ca38-513">*BlazorSample/Shared/* フォルダーに別のコンポーネント (`NavMenu.razor`) が存在する場合は、次の `@using` ステートメントを使用して `Index.razor` でコンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-513">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="1ca38-514">コンポーネントは、完全修飾名を使用して参照することもできます。この場合、 [`@using`](xref:mvc/views/razor#using)ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-514">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="1ca38-515">`global::` の修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-515">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="1ca38-516">エイリアス付き `using` ステートメント (`@using Foo = Bar`など) を含むコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-516">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="1ca38-517">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-517">Partially qualified names aren't supported.</span></span> <span data-ttu-id="1ca38-518">たとえば、`<Shared.NavMenu></Shared.NavMenu>` での `@using BlazorSample` の追加と `NavMenu.razor` の参照はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-518">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="1ca38-519">条件付き HTML 要素の属性</span><span class="sxs-lookup"><span data-stu-id="1ca38-519">Conditional HTML element attributes</span></span>

<span data-ttu-id="1ca38-520">HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-520">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="1ca38-521">値が `false` または `null`の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-521">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="1ca38-522">値が `true`場合は、属性が最小化されて表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-522">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="1ca38-523">次の例では、`IsCompleted` によって `checked` が要素のマークアップに表示されるかどうかが決まります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-523">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="1ca38-524">`IsCompleted` が `true`場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-524">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="1ca38-525">`IsCompleted` が `false`場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-525">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="1ca38-526">詳細については、「 <xref:mvc/views/razor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-526">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="1ca38-527">[Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)などの一部の HTML 属性は、.net 型が `bool`の場合、正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-527">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="1ca38-528">そのような場合は、`bool`ではなく `string` の型を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-528">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="1ca38-529">未加工の HTML</span><span class="sxs-lookup"><span data-stu-id="1ca38-529">Raw HTML</span></span>

<span data-ttu-id="1ca38-530">通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-530">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="1ca38-531">生の HTML をレンダリングするには、`MarkupString` 値で HTML コンテンツをラップします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-531">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="1ca38-532">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-532">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="1ca38-533">信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-533">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="1ca38-534">次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-534">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="1ca38-535">テンプレートコンポーネント</span><span class="sxs-lookup"><span data-stu-id="1ca38-535">Templated components</span></span>

<span data-ttu-id="1ca38-536">テンプレートコンポーネントは、1つまたは複数の UI テンプレートをパラメーターとして受け取り、コンポーネントのレンダリングロジックの一部として使用できるコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-536">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="1ca38-537">テンプレート化されたコンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-537">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="1ca38-538">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-538">A couple of examples include:</span></span>

* <span data-ttu-id="1ca38-539">テーブルのヘッダー、行、およびフッターのテンプレートをユーザーが指定できるようにするテーブルコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="1ca38-539">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="1ca38-540">リスト内の項目を表示するためのテンプレートをユーザーが指定できるようにするリストコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="1ca38-540">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="1ca38-541">テンプレート パラメーター</span><span class="sxs-lookup"><span data-stu-id="1ca38-541">Template parameters</span></span>

<span data-ttu-id="1ca38-542">`RenderFragment` または `RenderFragment<T>`型の1つ以上のコンポーネントパラメーターを指定して、テンプレート化されたコンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-542">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="1ca38-543">レンダーフラグメントは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-543">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="1ca38-544">`RenderFragment<T>` は、レンダリングフラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-544">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="1ca38-545">`TableTemplate` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1ca38-545">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="1ca38-546">テンプレート化されたコンポーネントを使用する場合、テンプレートパラメーターは、パラメーターの名前と一致する子要素を使用して指定できます (`TableHeader` と、次の例の `RowTemplate`)。</span><span class="sxs-lookup"><span data-stu-id="1ca38-546">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
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

### <a name="template-context-parameters"></a><span data-ttu-id="1ca38-547">テンプレートコンテキストパラメーター</span><span class="sxs-lookup"><span data-stu-id="1ca38-547">Template context parameters</span></span>

<span data-ttu-id="1ca38-548">要素として渡される型 `RenderFragment<T>` のコンポーネント引数には、`context` という名前の暗黙的なパラメーターがあります (たとえば、上記のコードサンプルでは `@context.PetId`)。ただし、子要素の `Context` 属性を使用してパラメーター名を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-548">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="1ca38-549">次の例では、`RowTemplate` 要素の `Context` 属性で `pet` パラメーターを指定しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-549">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
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

<span data-ttu-id="1ca38-550">または、コンポーネント要素の `Context` 属性を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-550">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="1ca38-551">指定された `Context` 属性は、指定されたすべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-551">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="1ca38-552">これは、暗黙的な子コンテンツ (ラップする子要素を持たない) のコンテンツパラメーター名を指定する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-552">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="1ca38-553">次の例では、`Context` 属性が `TableTemplate` 要素に表示され、すべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-553">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
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

### <a name="generic-typed-components"></a><span data-ttu-id="1ca38-554">汎用型のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="1ca38-554">Generic-typed components</span></span>

<span data-ttu-id="1ca38-555">多くの場合、テンプレート化されたコンポーネントは一般的に型指定されます</span><span class="sxs-lookup"><span data-stu-id="1ca38-555">Templated components are often generically typed.</span></span> <span data-ttu-id="1ca38-556">たとえば、汎用 `ListViewTemplate` コンポーネントを使用して `IEnumerable<T>` 値を表示できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-556">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="1ca38-557">ジェネリックコンポーネントを定義するには、 [`@typeparam`](xref:mvc/views/razor#typeparam)ディレクティブを使用して型パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-557">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="1ca38-558">ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-558">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="1ca38-559">それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-559">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="1ca38-560">次の例では、`TItem="Pet"` 型を指定しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-560">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="1ca38-561">カスケード値とパラメーター</span><span class="sxs-lookup"><span data-stu-id="1ca38-561">Cascading values and parameters</span></span>

<span data-ttu-id="1ca38-562">場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-562">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="1ca38-563">カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-563">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="1ca38-564">カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-564">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="1ca38-565">テーマの例</span><span class="sxs-lookup"><span data-stu-id="1ca38-565">Theme example</span></span>

<span data-ttu-id="1ca38-566">次のサンプルアプリの例では、`ThemeInfo` クラスが、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-566">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="1ca38-567">*UiThemeInfo/* :</span><span class="sxs-lookup"><span data-stu-id="1ca38-567">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="1ca38-568">先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-568">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="1ca38-569">`CascadingValue` コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-569">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="1ca38-570">たとえば、サンプルアプリでは、アプリのいずれかのレイアウトのテーマ情報 (`ThemeInfo`) を、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとして指定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-570">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="1ca38-571">`ButtonClass` には、レイアウトコンポーネントで `btn-success` の値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-571">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="1ca38-572">すべての子孫コンポーネントは、`ThemeInfo` カスケードオブジェクトを介してこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-572">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="1ca38-573">`CascadingValuesParametersLayout` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1ca38-573">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="1ca38-574">カスケード値を使用するために、コンポーネントは `[CascadingParameter]` 属性を使用してカスケード型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-574">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="1ca38-575">カスケード値は、型によってカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-575">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="1ca38-576">サンプルアプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値がカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-576">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="1ca38-577">パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-577">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="1ca38-578">`CascadingValuesParametersTheme` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1ca38-578">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="1ca38-579">同じサブツリー内で同じ型の複数の値を連鎖させるには、各 `CascadingValue` コンポーネントとそれに対応する `CascadingParameter`に一意の `Name` 文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-579">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="1ca38-580">次の例では、2つの `CascadingValue` コンポーネントが名前で `MyCascadingType` の異なるインスタンスをカスケードしています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-580">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="1ca38-581">子孫コンポーネントでは、カスケードされたパラメーターは、先祖コンポーネントの対応するカスケード値から名前で値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-581">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="1ca38-582">TabSet の例</span><span class="sxs-lookup"><span data-stu-id="1ca38-582">TabSet example</span></span>

<span data-ttu-id="1ca38-583">カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-583">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="1ca38-584">たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-584">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="1ca38-585">このサンプルアプリには、タブに実装されている `ITab` インターフェイスがあります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-585">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="1ca38-586">`CascadingValuesParametersTabSet` コンポーネントは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-586">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="1ca38-587">子 `Tab` コンポーネントは、パラメーターとして `TabSet`に明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-587">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="1ca38-588">代わりに、子 `Tab` コンポーネントは、`TabSet`の子コンテンツの一部になります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-588">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="1ca38-589">ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントは、*それ自体をカスケード値として提供*し、その後、子孫 `Tab` コンポーネントによって取得されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-589">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="1ca38-590">`TabSet` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1ca38-590">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="1ca38-591">子孫の `Tab` コンポーネントは、含まれている `TabSet` をカスケード型パラメーターとしてキャプチャするので、`Tab` のコンポーネントは、どのタブがアクティブであるかを `TabSet` および座標に追加します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-591">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="1ca38-592">`Tab` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1ca38-592">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="1ca38-593">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="1ca38-593">Razor templates</span></span>

<span data-ttu-id="1ca38-594">レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-594">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="1ca38-595">Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-595">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="1ca38-596">次の例は、`RenderFragment` と `RenderFragment<T>` の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-596">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="1ca38-597">レンダーフラグメントは、[テンプレートコンポーネント](#templated-components)に引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-597">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="1ca38-598">前のコードの出力をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-598">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="1ca38-599">手動の RenderTreeBuilder ロジック</span><span class="sxs-lookup"><span data-stu-id="1ca38-599">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="1ca38-600">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` には、コンポーネントと要素を操作するためのメソッドがC#用意されています</span><span class="sxs-lookup"><span data-stu-id="1ca38-600">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="1ca38-601">コンポーネントの作成に `RenderTreeBuilder` を使用することは、高度なシナリオです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-601">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="1ca38-602">形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-602">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="1ca38-603">次の `PetDetails` コンポーネントを検討してください。これは手動で別のコンポーネントに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-603">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="1ca38-604">次の例では、`CreateComponent` メソッドのループによって、3つの `PetDetails` コンポーネントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-604">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="1ca38-605">`RenderTreeBuilder` メソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-605">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="1ca38-606">Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-606">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="1ca38-607">`RenderTreeBuilder` メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-607">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="1ca38-608">**計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。**</span><span class="sxs-lookup"><span data-stu-id="1ca38-608">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="1ca38-609">詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-609">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="1ca38-610">`BuiltContent` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1ca38-610">`BuiltContent` component:</span></span>

```razor
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

> [!WARNING]
> <span data-ttu-id="1ca38-611">`Microsoft.AspNetCore.Components.RenderTree` の型により、レンダリング操作の*結果*を処理できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-611">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="1ca38-612">これらは、Blazor framework 実装の内部的な詳細です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-612">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="1ca38-613">これらの型は*不安定*であると見なされ、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-613">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="1ca38-614">シーケンス番号は、実行順序ではなくコード行番号に関連します</span><span class="sxs-lookup"><span data-stu-id="1ca38-614">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="1ca38-615">Blazor `.razor` ファイルは常にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-615">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="1ca38-616">コンパイル手順を使用すると、実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、`.razor` にとって大きな利点があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-616">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="1ca38-617">これらの機能強化の主要な例には、*シーケンス番号*が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-617">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="1ca38-618">シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-618">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="1ca38-619">ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-619">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="1ca38-620">次の Razor コンポーネント (*razor*) ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-620">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="1ca38-621">上記のコードは、次のようにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-621">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="1ca38-622">コードを初めて実行するときに、`someFlag` が `true`場合、ビルダーは次のメッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-622">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="1ca38-623">シーケンス</span><span class="sxs-lookup"><span data-stu-id="1ca38-623">Sequence</span></span> | <span data-ttu-id="1ca38-624">の型</span><span class="sxs-lookup"><span data-stu-id="1ca38-624">Type</span></span>      | <span data-ttu-id="1ca38-625">データ</span><span class="sxs-lookup"><span data-stu-id="1ca38-625">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1ca38-626">0</span><span class="sxs-lookup"><span data-stu-id="1ca38-626">0</span></span>        | <span data-ttu-id="1ca38-627">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="1ca38-627">Text node</span></span> | <span data-ttu-id="1ca38-628">First</span><span class="sxs-lookup"><span data-stu-id="1ca38-628">First</span></span>  |
| <span data-ttu-id="1ca38-629">1</span><span class="sxs-lookup"><span data-stu-id="1ca38-629">1</span></span>        | <span data-ttu-id="1ca38-630">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="1ca38-630">Text node</span></span> | <span data-ttu-id="1ca38-631">Second</span><span class="sxs-lookup"><span data-stu-id="1ca38-631">Second</span></span> |

<span data-ttu-id="1ca38-632">`someFlag` が `false`になり、マークアップが再び表示されるとします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-632">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="1ca38-633">この時点で、ビルダーは次のものを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-633">This time, the builder receives:</span></span>

| <span data-ttu-id="1ca38-634">シーケンス</span><span class="sxs-lookup"><span data-stu-id="1ca38-634">Sequence</span></span> | <span data-ttu-id="1ca38-635">の型</span><span class="sxs-lookup"><span data-stu-id="1ca38-635">Type</span></span>       | <span data-ttu-id="1ca38-636">データ</span><span class="sxs-lookup"><span data-stu-id="1ca38-636">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="1ca38-637">1</span><span class="sxs-lookup"><span data-stu-id="1ca38-637">1</span></span>        | <span data-ttu-id="1ca38-638">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="1ca38-638">Text node</span></span>  | <span data-ttu-id="1ca38-639">Second</span><span class="sxs-lookup"><span data-stu-id="1ca38-639">Second</span></span> |

<span data-ttu-id="1ca38-640">ランタイムが diff を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な*編集スクリプト*が生成されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-640">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="1ca38-641">最初のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-641">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="1ca38-642">プログラムによってシーケンス番号を生成した場合の問題</span><span class="sxs-lookup"><span data-stu-id="1ca38-642">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="1ca38-643">代わりに、次のレンダーツリービルダーのロジックを記述したとします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-643">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="1ca38-644">これで、最初の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-644">Now, the first output is:</span></span>

| <span data-ttu-id="1ca38-645">シーケンス</span><span class="sxs-lookup"><span data-stu-id="1ca38-645">Sequence</span></span> | <span data-ttu-id="1ca38-646">の型</span><span class="sxs-lookup"><span data-stu-id="1ca38-646">Type</span></span>      | <span data-ttu-id="1ca38-647">データ</span><span class="sxs-lookup"><span data-stu-id="1ca38-647">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1ca38-648">0</span><span class="sxs-lookup"><span data-stu-id="1ca38-648">0</span></span>        | <span data-ttu-id="1ca38-649">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="1ca38-649">Text node</span></span> | <span data-ttu-id="1ca38-650">First</span><span class="sxs-lookup"><span data-stu-id="1ca38-650">First</span></span>  |
| <span data-ttu-id="1ca38-651">1</span><span class="sxs-lookup"><span data-stu-id="1ca38-651">1</span></span>        | <span data-ttu-id="1ca38-652">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="1ca38-652">Text node</span></span> | <span data-ttu-id="1ca38-653">Second</span><span class="sxs-lookup"><span data-stu-id="1ca38-653">Second</span></span> |

<span data-ttu-id="1ca38-654">この結果は前のケースと同じであるため、負の問題は存在しません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-654">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="1ca38-655">2番目のレンダリングでは `someFlag` が `false`、出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-655">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="1ca38-656">シーケンス</span><span class="sxs-lookup"><span data-stu-id="1ca38-656">Sequence</span></span> | <span data-ttu-id="1ca38-657">の型</span><span class="sxs-lookup"><span data-stu-id="1ca38-657">Type</span></span>      | <span data-ttu-id="1ca38-658">データ</span><span class="sxs-lookup"><span data-stu-id="1ca38-658">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="1ca38-659">0</span><span class="sxs-lookup"><span data-stu-id="1ca38-659">0</span></span>        | <span data-ttu-id="1ca38-660">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="1ca38-660">Text node</span></span> | <span data-ttu-id="1ca38-661">Second</span><span class="sxs-lookup"><span data-stu-id="1ca38-661">Second</span></span> |

<span data-ttu-id="1ca38-662">今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-662">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="1ca38-663">最初のテキストノードの値を `Second`に変更します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-663">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="1ca38-664">2番目のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-664">Remove the second text node.</span></span>

<span data-ttu-id="1ca38-665">シーケンス番号を生成すると、`if/else` 分岐とループが元のコードに存在する場所に関する有用な情報がすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-665">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="1ca38-666">これにより、diff は前の**2 倍**になります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-666">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="1ca38-667">これは簡単な例です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-667">This is a trivial example.</span></span> <span data-ttu-id="1ca38-668">複雑で深く入れ子になった構造、特にループを使用したより現実的なケースでは、パフォーマンスコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-668">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="1ca38-669">どのループブロックまたは分岐が挿入または削除されたかをすぐに識別するのではなく、diff アルゴリズムでは、レンダリングツリーに深く再帰する必要があります相互に関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-669">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="1ca38-670">ガイダンスと結論</span><span class="sxs-lookup"><span data-stu-id="1ca38-670">Guidance and conclusions</span></span>

* <span data-ttu-id="1ca38-671">シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-671">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="1ca38-672">コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-672">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="1ca38-673">手動で実装された `RenderTreeBuilder` ロジックの長いブロックは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-673">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="1ca38-674">`.razor` ファイルを優先し、コンパイラがシーケンス番号を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-674">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="1ca38-675">手動の `RenderTreeBuilder` ロジックを回避できない場合は、長いブロックのコードを `OpenRegion`/`CloseRegion` 呼び出しでラップされた小さな部分に分割します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-675">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="1ca38-676">各リージョンにはシーケンス番号の個別のスペースがあるため、各リージョン内のゼロ (または任意の任意の数) から再起動できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-676">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="1ca38-677">シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-677">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="1ca38-678">初期値とギャップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-678">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="1ca38-679">正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。</span><span class="sxs-lookup"><span data-stu-id="1ca38-679">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="1ca38-680"> はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-680"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="1ca38-681">シーケンス番号を使用すると比較ははるかに高速になり*ます。* また、Blazor には、開発者が razor ファイルを作成するときにシーケンス番号を自動的に処理するコンパイル手順の利点があります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-681">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="1ca38-682">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1ca38-682">Localization</span></span>

Blazor<span data-ttu-id="1ca38-683"> サーバーアプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-683"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="1ca38-684">ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-684">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="1ca38-685">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-685">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="1ca38-686">クッキー</span><span class="sxs-lookup"><span data-stu-id="1ca38-686">Cookies</span></span>](#cookies)
* [<span data-ttu-id="1ca38-687">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="1ca38-687">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="1ca38-688">使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-688">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="1ca38-689">国際化のためのリンカーの構成 (Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1ca38-689">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="1ca38-690">既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-690">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="1ca38-691">リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-691">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="1ca38-692">Cookies</span><span class="sxs-lookup"><span data-stu-id="1ca38-692">Cookies</span></span>

<span data-ttu-id="1ca38-693">ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-693">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="1ca38-694">Cookie は、アプリのホストページ (*Pages/host. cshtml. .cs*) の `OnGet` メソッドによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-694">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="1ca38-695">ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-695">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="1ca38-696">Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。</span><span class="sxs-lookup"><span data-stu-id="1ca38-696">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="1ca38-697">ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-697">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="1ca38-698">したがって、ローカライズカルチャ cookie を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-698">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="1ca38-699">カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-699">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="1ca38-700">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-700">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="1ca38-701">次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-701">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="1ca38-702">Blazor Server アプリで次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-702">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="1ca38-703">ローカライズはアプリで処理されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-703">Localization is handled in the app:</span></span>

1. <span data-ttu-id="1ca38-704">ブラウザーは、アプリに最初の HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-704">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="1ca38-705">カルチャは、ローカリゼーションミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-705">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="1ca38-706">*_Host*の `OnGet` メソッドは、応答の一部として cookie 内のカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-706">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="1ca38-707">ブラウザーは、WebSocket 接続を開き、対話型の Blazor サーバーセッションを作成します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-707">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="1ca38-708">ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-708">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="1ca38-709">Blazor サーバーセッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-709">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="1ca38-710">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="1ca38-710">Provide UI to choose the culture</span></span>

<span data-ttu-id="1ca38-711">ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-711">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="1ca38-712">このプロセスは、ユーザーがセキュリティで保護されたリソースにアクセスしようとしたときに、ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされる&mdash;、web アプリで発生する処理に似ています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-712">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="1ca38-713">アプリは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-713">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="1ca38-714">コントローラーは、ユーザーが選択したカルチャを cookie に設定し、ユーザーを元の URI にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="1ca38-714">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="1ca38-715">Cookie でユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するために、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-715">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="1ca38-716">`LocalRedirect` アクションの結果を使用して、開いているリダイレクト攻撃を防止します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-716">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="1ca38-717">詳細については、「 <xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-717">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="1ca38-718">次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-718">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="1ca38-719">Blazor アプリで .NET ローカライズシナリオを使用する</span><span class="sxs-lookup"><span data-stu-id="1ca38-719">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="1ca38-720">Blazor アプリ内では、次の .NET ローカリゼーションとグローバリゼーションのシナリオを利用できます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-720">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="1ca38-721">.NET のリソースシステム</span><span class="sxs-lookup"><span data-stu-id="1ca38-721">.NET's resources system</span></span>
* <span data-ttu-id="1ca38-722">カルチャ固有の数値と日付の書式設定</span><span class="sxs-lookup"><span data-stu-id="1ca38-722">Culture-specific number and date formatting</span></span>

Blazor<span data-ttu-id="1ca38-723">の `@bind` 機能は、ユーザーの現在のカルチャに基づいてグローバリゼーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="1ca38-723">'s `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="1ca38-724">詳細については、「[データバインディング](#data-binding)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-724">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="1ca38-725">現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-725">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="1ca38-726">`IStringLocalizer<>` は Blazor アプリで*サポートされて*います。</span><span class="sxs-lookup"><span data-stu-id="1ca38-726">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="1ca38-727">`IHtmlLocalizer<>`、`IViewLocalizer<>`、データ注釈のローカライズは MVC シナリオ ASP.NET Core、Blazor アプリではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="1ca38-727">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="1ca38-728">詳細については、「 <xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ca38-728">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="1ca38-729">スケーラブルベクターグラフィックス (SVG) イメージ</span><span class="sxs-lookup"><span data-stu-id="1ca38-729">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="1ca38-730">Blazor は HTML をレンダリングするため、スケーラブルベクターグラフィックス (svg) イメージ (*svg*) を含むブラウザーでサポートされているイメージは、`<img>` タグを介してサポートされます。</span><span class="sxs-lookup"><span data-stu-id="1ca38-730">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="1ca38-731">同様に、SVG イメージは、スタイルシートファイル ( *.css*) の css 規則でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-731">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="1ca38-732">ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-732">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="1ca38-733">コンポーネントファイル (*razor*) に `<svg>` タグを直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-733">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="1ca38-734">たとえば、`<use>` タグは現在尊重されていないため、`@bind` をいくつかの SVG タグと共に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="1ca38-734">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="1ca38-735">今後のリリースでは、これらの制限に対処する予定です。</span><span class="sxs-lookup"><span data-stu-id="1ca38-735">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ca38-736">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1ca38-736">Additional resources</span></span>

* <span data-ttu-id="1ca38-737"><xref:security/blazor/server> &ndash; には、リソース枯渇に対処する必要がある Blazor サーバーアプリを構築するためのガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1ca38-737"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
