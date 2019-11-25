---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
uid: blazor/components
ms.openlocfilehash: 267a6f5aa96feeecc280238abbef86949750b07e
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317215"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="ceb71-103">ASP.NET Core Razor コンポーネントを作成して使用する</span><span class="sxs-lookup"><span data-stu-id="ceb71-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="ceb71-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ceb71-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ceb71-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ceb71-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="ceb71-106"> アプリは*コンポーネント*を使用して構築されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-106"> apps are built using *components*.</span></span> <span data-ttu-id="ceb71-107">コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="ceb71-108">コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="ceb71-109">コンポーネントは柔軟で軽量です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="ceb71-110">入れ子にして再利用したり、プロジェクト間で共有したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="ceb71-111">コンポーネントクラス</span><span class="sxs-lookup"><span data-stu-id="ceb71-111">Component classes</span></span>

<span data-ttu-id="ceb71-112">コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="ceb71-113">Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="ceb71-114">コンポーネントの名前は、大文字で始まる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="ceb71-115">たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="ceb71-116">コンポーネントの UI は、HTML を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="ceb71-117">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="ceb71-118">アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="ceb71-119">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="ceb71-120">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="ceb71-121">`@code` ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッド、またはその他のコンポーネントロジックを定義するために指定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="ceb71-122">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="ceb71-123">ASP.NET Core 3.0 の以前のプレビューでは、`@functions` ブロックは Razor コンポーネントの `@code` ブロックと同じ目的で使用されていました。</span><span class="sxs-lookup"><span data-stu-id="ceb71-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="ceb71-124">`@functions` ブロックは Razor コンポーネントで引き続き機能しますが、ASP.NET Core 3.0 Preview 6 以降では `@code` ブロックを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="ceb71-125">コンポーネントメンバーは、`@`で始まる式を使用してC# 、コンポーネントのレンダリングロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="ceb71-126">たとえば、 C#フィールド名をプレフィックス `@` によって表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="ceb71-127">次の例では、が評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="ceb71-128">`font-style`の CSS プロパティ値に `_headingFontStyle` します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="ceb71-129">`<h1>` 要素の内容に `_headingText` します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="ceb71-130">コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="ceb71-131"> は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-131"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="ceb71-132">コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="ceb71-133">Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="ceb71-134">ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="ceb71-135">カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を親コンポーネントまたはアプリの *_Imports razor*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="ceb71-136">たとえば、次の名前空間は、アプリのルート名前空間が `WebApplication`場合*に、コンポーネントフォルダー内*のコンポーネントを使用可能にします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="ceb71-137">コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="ceb71-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="ceb71-138">既存の Razor Pages および MVC アプリでコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="ceb71-139">Razor コンポーネントを使用するために既存のページやビューを書き直す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="ceb71-140">ページまたはビューが表示されると、コンポーネントは同時に prerendered されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="ceb71-141">ページまたはビューからコンポーネントを表示するには、`Component` タグヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-141">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="ceb71-142">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-142">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="ceb71-143">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-143">Is prerendered into the page.</span></span>
* <span data-ttu-id="ceb71-144">は、ページに静的な HTML として表示されるか、またはユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-144">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="ceb71-145">[説明]</span><span class="sxs-lookup"><span data-stu-id="ceb71-145">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="ceb71-146">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-146">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ceb71-147">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="ceb71-148">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-148">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ceb71-149">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-149">Output from the component isn't included.</span></span> <span data-ttu-id="ceb71-150">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-150">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="ceb71-151">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-151">Renders the component into static HTML.</span></span> |

<span data-ttu-id="ceb71-152">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-152">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="ceb71-153">コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-153">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="ceb71-154">コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-154">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="ceb71-155">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-155">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="ceb71-156">コンポーネントのレンダリング方法、コンポーネントの状態、および `Component` タグヘルパーの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-156">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

<span data-ttu-id="ceb71-157">ページまたはビューからコンポーネントを表示するには、`RenderComponentAsync<TComponent>` HTML ヘルパーメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-157">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
```

<span data-ttu-id="ceb71-158">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-158">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="ceb71-159">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-159">Is prerendered into the page.</span></span>
* <span data-ttu-id="ceb71-160">は、ページに静的な HTML として表示されるか、またはユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-160">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="ceb71-161">[説明]</span><span class="sxs-lookup"><span data-stu-id="ceb71-161">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="ceb71-162">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-162">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ceb71-163">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-163">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="ceb71-164">パラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-164">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="ceb71-165">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-165">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ceb71-166">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-166">Output from the component isn't included.</span></span> <span data-ttu-id="ceb71-167">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-167">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="ceb71-168">パラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-168">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="ceb71-169">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-169">Renders the component into static HTML.</span></span> <span data-ttu-id="ceb71-170">パラメーターがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-170">Parameters are supported.</span></span> |

<span data-ttu-id="ceb71-171">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-171">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="ceb71-172">コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-172">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="ceb71-173">コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-173">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="ceb71-174">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-174">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="ceb71-175">コンポーネントのレンダリング方法、コンポーネントの状態、および `RenderComponentAsync` HTML ヘルパーの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-175">For more information on how components are rendered, component state, and the `RenderComponentAsync` HTML Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

## <a name="use-components"></a><span data-ttu-id="ceb71-176">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="ceb71-176">Use components</span></span>

<span data-ttu-id="ceb71-177">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-177">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="ceb71-178">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-178">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="ceb71-179">属性のバインドでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-179">Attribute binding is case sensitive.</span></span> <span data-ttu-id="ceb71-180">たとえば、`@bind` が有効で、`@Bind` が無効です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-180">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="ceb71-181">*Index. razor*の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-181">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="ceb71-182">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-182">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="ceb71-183">コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-183">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="ceb71-184">コンポーネントの名前空間に `@using` ステートメントを追加すると、コンポーネントが使用可能になり、警告が削除されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-184">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="ceb71-185">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="ceb71-185">Component parameters</span></span>

<span data-ttu-id="ceb71-186">コンポーネントには、コンポーネントクラスのパブリックプロパティを使用して定義されるコンポーネント*パラメーター*を含めることができます。これは、`[Parameter]` 属性を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-186">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="ceb71-187">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="ceb71-187">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="ceb71-188">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-188">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="ceb71-189">次の例では、`ParentComponent` によって `ChildComponent`の `Title` プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-189">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="ceb71-190">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-190">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="ceb71-191">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="ceb71-191">Child content</span></span>

<span data-ttu-id="ceb71-192">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-192">Components can set the content of another component.</span></span> <span data-ttu-id="ceb71-193">割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-193">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="ceb71-194">次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す `RenderFragment`を表す `ChildContent` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-194">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="ceb71-195">`ChildContent` の値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップに配置されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-195">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="ceb71-196">`ChildContent` の値は、親コンポーネントから受信され、ブートストラップパネルの `panel-body`内に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-196">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="ceb71-197">*Components/ChildComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-197">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="ceb71-198">`RenderFragment` コンテンツを受け取るプロパティには、規約によって `ChildContent` 名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-198">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="ceb71-199">次の `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` を表示するためのコンテンツを提供できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-199">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="ceb71-200">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-200">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="ceb71-201">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="ceb71-201">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="ceb71-202">コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-202">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="ceb71-203">Splatted Razor ディレクティブ[@attributes](xref:mvc/views/razor#attributes)を使用してコンポーネントがレンダリングされるときに、ディクショナリ内で追加の属性をキャプチャし、要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-203">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="ceb71-204">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-204">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="ceb71-205">たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-205">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="ceb71-206">次の例では、最初の `<input>` 要素 (`id="useIndividualParams"`) は個々のコンポーネントパラメーターを使用し、2番目の `<input>` 要素 (`id="useAttributesDict"`) は属性スプラッティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-206">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="ceb71-207">パラメーターの型は、文字列キーを使用して `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-207">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="ceb71-208">このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-208">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="ceb71-209">両方の方法を使用して表示される `<input>` 要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-209">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="ceb71-210">任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true`に設定して、`[Parameter]` 属性を使用してコンポーネントパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-210">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="ceb71-211">`[Parameter]` の `CaptureUnmatchedValues` プロパティを使用すると、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-211">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="ceb71-212">コンポーネントは、`CaptureUnmatchedValues`で1つのパラメーターのみを定義できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-212">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="ceb71-213">`CaptureUnmatchedValues` で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-213">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="ceb71-214">このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` もオプションです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-214">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="ceb71-215">要素属性の位置を基準とした `@attributes` の位置が重要です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-215">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="ceb71-216">要素に対して `@attributes` が splatted されると、属性は右から左 (最後から順) に処理されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-216">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="ceb71-217">`Child` コンポーネントを使用するコンポーネントの次の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-217">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="ceb71-218">*Parentcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-218">*ParentComponent.razor*:</span></span>

```cshtml
<ChildComponent extra="10" />
```

<span data-ttu-id="ceb71-219">*Childcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-219">*ChildComponent.razor*:</span></span>

```cshtml
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="ceb71-220">`Child` コンポーネントの `extra` 属性が `@attributes`の右側に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-220">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="ceb71-221">属性が右から左に処理されるため、`Parent` コンポーネントのレンダリングされる `<div>` には `extra="5"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-221">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="ceb71-222">次の例では、`Child` コンポーネントの `<div>`で `extra` と `@attributes` の順序が逆になっています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-222">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="ceb71-223">*Parentcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-223">*ParentComponent.razor*:</span></span>

```cshtml
<ChildComponent extra="10" />
```

<span data-ttu-id="ceb71-224">*Childcomponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-224">*ChildComponent.razor*:</span></span>

```cshtml
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="ceb71-225">`Parent` コンポーネントに表示される `<div>` には、追加の属性を通じて渡された `extra="10"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-225">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="ceb71-226">データ バインディング</span><span class="sxs-lookup"><span data-stu-id="ceb71-226">Data binding</span></span>

<span data-ttu-id="ceb71-227">コンポーネントと DOM 要素の両方に対するデータバインディングは、 [@bind](xref:mvc/views/razor#bind)属性を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-227">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="ceb71-228">次の例では、`CurrentValue` プロパティをテキストボックスの値にバインドします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-228">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```cshtml
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="ceb71-229">テキストボックスがフォーカスを失うと、プロパティの値が更新されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-229">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="ceb71-230">テキストボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-230">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="ceb71-231">イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、*通常*、イベントハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-231">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="ceb71-232">`CurrentValue` プロパティ (`<input @bind="CurrentValue" />`) で `@bind` を使用することは、基本的に次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-232">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="ceb71-233">コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-233">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="ceb71-234">ユーザーがテキストボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-234">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="ceb71-235">実際には、型変換が実行されるケースが `@bind` によって処理されるため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-235">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="ceb71-236">原則として、`@bind` は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-236">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="ceb71-237">`@bind` 構文を使用した `onchange` イベントの処理に加えて、`event` パラメーター ([@bind-value:event](xref:mvc/views/razor#bind)) を使用して[@bind-value](xref:mvc/views/razor#bind)属性を指定することで、プロパティまたはフィールドを他のイベントを使用してバインドできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-237">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="ceb71-238">次の例では、`oninput` イベントの `CurrentValue` プロパティをバインドします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-238">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="ceb71-239">要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキストボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-239">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="ceb71-240">**解析不可能値**</span><span class="sxs-lookup"><span data-stu-id="ceb71-240">**Unparsable values**</span></span>

<span data-ttu-id="ceb71-241">データバインド要素に解析できない値を指定すると、バインドイベントがトリガーされたときに、解析されていない値が自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-241">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="ceb71-242">次のシナリオについて検討してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-242">Consider the following scenario:</span></span>

* <span data-ttu-id="ceb71-243">`<input>` 要素は、初期値 `123`を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-243">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="ceb71-244">ユーザーは、要素の値をページの `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-244">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="ceb71-245">前のシナリオでは、要素の値が `123`に戻されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-245">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="ceb71-246">`123`の元の値を優先して `123.45` 値が拒否された場合、ユーザーはその値が受け入れられていないことを認識します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-246">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="ceb71-247">既定では、バインドは要素の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) に適用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-247">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="ceb71-248">別のイベントを設定するには、`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-248">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="ceb71-249">`oninput` イベント (`@bind-value:event="oninput"`) の場合、解析できない値を導入するキーストロークの後に再設定が発生します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-249">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="ceb71-250">`int`バインドされた型の `oninput` イベントを対象とする場合、ユーザーは `.` 文字を入力できません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-250">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="ceb71-251">`.` 文字はすぐに削除されるので、ユーザーは、整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-251">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="ceb71-252">`oninput` イベントの値を元に戻すことは、ユーザーが解析できない `<input>` 値のクリアを許可する必要がある場合など、理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-252">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="ceb71-253">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-253">Alternatives include:</span></span>

* <span data-ttu-id="ceb71-254">`oninput` イベントは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-254">Don't use the `oninput` event.</span></span> <span data-ttu-id="ceb71-255">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-255">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="ceb71-256">`int?` や `string`などの null 許容型にバインドし、無効なエントリを処理するカスタムロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-256">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="ceb71-257">`InputNumber` や `InputDate`などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-257">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="ceb71-258">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-258">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="ceb71-259">フォーム検証コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ceb71-259">Form validation components:</span></span>
  * <span data-ttu-id="ceb71-260">ユーザーが無効な入力を提供し、関連付けられた `EditContext`で検証エラーを受信することを許可します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-260">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="ceb71-261">追加の web フォームデータを入力するユーザーに干渉することなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-261">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="ceb71-262">**グローバリゼーション**</span><span class="sxs-lookup"><span data-stu-id="ceb71-262">**Globalization**</span></span>

<span data-ttu-id="ceb71-263">`@bind` 値は、現在のカルチャの規則を使用して表示および解析するように書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-263">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="ceb71-264">現在のカルチャには、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-264">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="ceb71-265">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-265">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="ceb71-266">上記のフィールド型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-266">The preceding field types:</span></span>

* <span data-ttu-id="ceb71-267">は、適切なブラウザーベースの書式規則を使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-267">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="ceb71-268">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-268">Can't contain free-form text.</span></span>
* <span data-ttu-id="ceb71-269">ブラウザーの実装に基づいてユーザーの操作特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-269">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="ceb71-270">次のフィールド型には特定の書式設定要件がありますが、Blazor で現在サポートされていないのは、すべての主要なブラウザーでサポートされていないためです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-270">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="ceb71-271">`@bind` では、`@bind:culture` パラメーターを使用して、値の解析および書式設定のための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-271">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="ceb71-272">`date` および `number` のフィールドの種類を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-272">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="ceb71-273">`date` と `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-273">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="ceb71-274">ユーザーのカルチャを設定する方法については、「[ローカリゼーション](#localization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-274">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="ceb71-275">**書式指定文字列**</span><span class="sxs-lookup"><span data-stu-id="ceb71-275">**Format strings**</span></span>

<span data-ttu-id="ceb71-276">データバインディングは、 [@bind:format](xref:mvc/views/razor#bind)を使用して <xref:System.DateTime> 書式指定文字列で動作します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-276">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="ceb71-277">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-277">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="ceb71-278">前のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text`に設定されています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-278">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="ceb71-279">`@bind:format` は、次の .NET 型のバインドに対してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-279">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="ceb71-280"><xref:System.DateTime?displayProperty=fullName> ですか。</span><span class="sxs-lookup"><span data-stu-id="ceb71-280"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="ceb71-281"><xref:System.DateTimeOffset?displayProperty=fullName> ですか。</span><span class="sxs-lookup"><span data-stu-id="ceb71-281"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="ceb71-282">`@bind:format` 属性は、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-282">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="ceb71-283">この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-283">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="ceb71-284">Blazor に日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-284">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="ceb71-285">**コンポーネントのパラメーター**</span><span class="sxs-lookup"><span data-stu-id="ceb71-285">**Component parameters**</span></span>

<span data-ttu-id="ceb71-286">バインディングはコンポーネントパラメーターを認識しますが、`@bind-{property}` はコンポーネント間でプロパティ値をバインドできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-286">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="ceb71-287">次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネントパラメーターと `YearChanged` コールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-287">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="ceb71-288">`EventCallback<T>` については、「 [Eventcallback](#eventcallback) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-288">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="ceb71-289">次の親コンポーネントは `ChildComponent` を使用し、親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-289">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="ceb71-290">`ParentComponent` を読み込むと、次のマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-290">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="ceb71-291">`ParentComponent`のボタンを選択して `ParentYear` プロパティの値が変更された場合は、`ChildComponent` の `Year` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-291">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="ceb71-292">`Year` の新しい値は、`ParentComponent` が再度実行されたときに UI に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-292">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="ceb71-293">`Year` パラメーターは、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-293">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="ceb71-294">慣例により、`<ChildComponent @bind-Year="ParentYear" />` は基本的には書き込みと同じです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-294">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="ceb71-295">一般に、プロパティは、`@bind-property:event` 属性を使用して、対応するイベントハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-295">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="ceb71-296">たとえば、プロパティ `MyProp` は、次の2つの属性を使用して `MyEventHandler` にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-296">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="ceb71-297">イベント処理</span><span class="sxs-lookup"><span data-stu-id="ceb71-297">Event handling</span></span>

<span data-ttu-id="ceb71-298">Razor コンポーネントは、イベント処理機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-298">Razor components provide event handling features.</span></span> <span data-ttu-id="ceb71-299">`on{EVENT}` という名前の HTML 要素属性 (`onclick`、`onsubmit`など) とデリゲート型の値がある場合、Razor コンポーネントはその属性の値をイベントハンドラーとして扱います。</span><span class="sxs-lookup"><span data-stu-id="ceb71-299">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="ceb71-300">属性の名前は、常に[{EVENT}@on](xref:mvc/views/razor#onevent)書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-300">The attribute's name is always formatted [@on{EVENT}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="ceb71-301">次のコードは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-301">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="ceb71-302">次のコードは、UI でチェックボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-302">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="ceb71-303">イベントハンドラーを非同期にして、<xref:System.Threading.Tasks.Task>を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-303">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="ceb71-304">`StateHasChanged()`を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-304">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="ceb71-305">例外は、発生するとログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-305">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="ceb71-306">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-306">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

### <a name="event-argument-types"></a><span data-ttu-id="ceb71-307">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="ceb71-307">Event argument types</span></span>

<span data-ttu-id="ceb71-308">イベントによっては、イベント引数の型が許可されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-308">For some events, event argument types are permitted.</span></span> <span data-ttu-id="ceb71-309">これらのイベントの種類のいずれかにアクセスする必要がない場合は、メソッドの呼び出しで必要とされません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-309">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="ceb71-310">次の表に、サポートされている `EventArgs` を示します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-310">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="ceb71-311">Event</span><span class="sxs-lookup"><span data-stu-id="ceb71-311">Event</span></span>            | <span data-ttu-id="ceb71-312">インスタンス</span><span class="sxs-lookup"><span data-stu-id="ceb71-312">Class</span></span>                | <span data-ttu-id="ceb71-313">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="ceb71-313">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="ceb71-314">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="ceb71-314">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="ceb71-315">`oncut`、`oncopy`、`onpaste`</span><span class="sxs-lookup"><span data-stu-id="ceb71-315">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="ceb71-316">抗力</span><span class="sxs-lookup"><span data-stu-id="ceb71-316">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="ceb71-317">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="ceb71-317">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="ceb71-318">`DataTransfer` および `DataTransferItem` ドラッグした項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-318">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="ceb71-319">[エラー]</span><span class="sxs-lookup"><span data-stu-id="ceb71-319">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="ceb71-320">Event</span><span class="sxs-lookup"><span data-stu-id="ceb71-320">Event</span></span>            | `EventArgs`          | <span data-ttu-id="ceb71-321">*General*</span><span class="sxs-lookup"><span data-stu-id="ceb71-321">*General*</span></span><br><span data-ttu-id="ceb71-322">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="ceb71-322">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="ceb71-323">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="ceb71-323">*Clipboard*</span></span><br><span data-ttu-id="ceb71-324">`onbeforecut`、`onbeforecopy`、`onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="ceb71-324">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="ceb71-325">*入力*</span><span class="sxs-lookup"><span data-stu-id="ceb71-325">*Input*</span></span><br><span data-ttu-id="ceb71-326">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="ceb71-326">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="ceb71-327">*メディア*</span><span class="sxs-lookup"><span data-stu-id="ceb71-327">*Media*</span></span><br><span data-ttu-id="ceb71-328">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`の `onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、および `onsuspend``ontimeupdate``onvolumechange``onwaiting`</span><span class="sxs-lookup"><span data-stu-id="ceb71-328">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="ceb71-329">フォーカス</span><span class="sxs-lookup"><span data-stu-id="ceb71-329">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="ceb71-330">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="ceb71-330">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="ceb71-331">には `relatedTarget`のサポートは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-331">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="ceb71-332">入力</span><span class="sxs-lookup"><span data-stu-id="ceb71-332">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="ceb71-333">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="ceb71-333">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="ceb71-334">キーボード</span><span class="sxs-lookup"><span data-stu-id="ceb71-334">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="ceb71-335">`onkeydown`、`onkeypress`、`onkeyup`</span><span class="sxs-lookup"><span data-stu-id="ceb71-335">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="ceb71-336">マウス</span><span class="sxs-lookup"><span data-stu-id="ceb71-336">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="ceb71-337">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="ceb71-337">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="ceb71-338">マウスポインター</span><span class="sxs-lookup"><span data-stu-id="ceb71-338">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="ceb71-339">`onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="ceb71-339">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="ceb71-340">マウスホイール</span><span class="sxs-lookup"><span data-stu-id="ceb71-340">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="ceb71-341">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="ceb71-341">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="ceb71-342">進行状況</span><span class="sxs-lookup"><span data-stu-id="ceb71-342">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="ceb71-343">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="ceb71-343">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="ceb71-344">タッチ</span><span class="sxs-lookup"><span data-stu-id="ceb71-344">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="ceb71-345">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="ceb71-345">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="ceb71-346">`TouchPoint` は、タッチを区別するデバイス上の1つの連絡先ポイントを表します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-346">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="ceb71-347">前の表に示したイベントのプロパティとイベント処理動作の詳細については、「[参照ソースの EventArgs クラス (aspnet/AspNetCore release/3.0 分岐)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-347">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="ceb71-348">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="ceb71-348">Lambda expressions</span></span>

<span data-ttu-id="ceb71-349">ラムダ式を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-349">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="ceb71-350">多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-350">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="ceb71-351">次の例では、3つのボタンを作成します。各ボタンは、UI で選択されたときに、イベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-351">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="ceb71-352">ラムダ式では、`for` ループでループ変数 (`i`) を**直接使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="ceb71-352">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="ceb71-353">それ以外の場合、すべてのラムダ式で同じ変数が使用され、`i`の値はすべてのラムダで同じになります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-353">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="ceb71-354">常にローカル変数 (前の例では`buttonNumber`) で値をキャプチャし、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-354">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="ceb71-355">EventCallback</span><span class="sxs-lookup"><span data-stu-id="ceb71-355">EventCallback</span></span>

<span data-ttu-id="ceb71-356">入れ子になったコンポーネントの一般的なシナリオとして、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行することが望まれます。たとえば、子で `onclick` イベントが発生した場合などです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-356">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="ceb71-357">コンポーネント間でイベントを公開するには、`EventCallback`を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-357">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="ceb71-358">親コンポーネントは、コールバックメソッドを子コンポーネントの `EventCallback`に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-358">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="ceb71-359">サンプルアプリの `ChildComponent` は、ボタンの `onclick` ハンドラーが、サンプルの `ParentComponent`から `EventCallback` デリゲートを受け取るように設定されている方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-359">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="ceb71-360">`EventCallback` は `MouseEventArgs`で入力されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-360">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="ceb71-361">`ParentComponent` は、子の `EventCallback<T>` を `ShowMessage` メソッドに設定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-361">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="ceb71-362">`ChildComponent`でボタンが選択されている場合:</span><span class="sxs-lookup"><span data-stu-id="ceb71-362">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="ceb71-363">`ParentComponent`の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-363">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="ceb71-364">`messageText` が更新され、`ParentComponent`に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-364">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="ceb71-365">`StateHasChanged` の呼び出しは、コールバックのメソッド (`ShowMessage`) には必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-365">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="ceb71-366">`StateHasChanged` は、子イベントと同様に、子の内部で実行されるイベントハンドラーでコンポーネントの実行がトリガーされるのと同様に、`ParentComponent`をレンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-366">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="ceb71-367">`EventCallback` および `EventCallback<T>` 非同期デリゲートを許可します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-367">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="ceb71-368">`EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-368">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="ceb71-369">`EventCallback` は弱く型指定され、任意の引数型を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-369">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="ceb71-370">`InvokeAsync` で `EventCallback` または `EventCallback<T>` を呼び出し、<xref:System.Threading.Tasks.Task>を待機します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-370">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="ceb71-371">イベント処理とバインドコンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-371">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="ceb71-372">厳密に型指定された `EventCallback<T>` `EventCallback`を優先します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-372">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="ceb71-373">`EventCallback<T>` は、コンポーネントのユーザーに対してより適切なエラーフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-373">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="ceb71-374">他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-374">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="ceb71-375">コールバックに値が渡されない場合は、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-375">Use `EventCallback` when there's no value passed to the callback.</span></span>

::: moniker range=">= aspnetcore-3.1"

### <a name="prevent-default-actions"></a><span data-ttu-id="ceb71-376">既定のアクションを禁止する</span><span class="sxs-lookup"><span data-stu-id="ceb71-376">Prevent default actions</span></span>

<span data-ttu-id="ceb71-377">イベントの既定のアクションを実行しないようにするには、 [@on{EVENT}:P reventDefault](xref:mvc/views/razor#oneventpreventdefault)ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-377">Use the [@on{EVENT}:preventDefault](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="ceb71-378">入力デバイスでキーが選択され、要素のフォーカスがテキストボックスに表示されている場合は、通常、ブラウザーによってテキストボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-378">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="ceb71-379">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作を回避できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-379">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="ceb71-380">カウンターがインクリメントされ、 **+** キーが `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-380">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```cshtml
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

<span data-ttu-id="ceb71-381">値を指定せずに `@on{EVENT}:preventDefault` 属性を指定することは `@on{EVENT}:preventDefault="true"`と同じです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-381">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="ceb71-382">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-382">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="ceb71-383">次の例では、`_shouldPreventDefault` は `true` または `false`のいずれかに設定された `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-383">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```cshtml
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="ceb71-384">既定のアクションを防ぐために、イベントハンドラーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-384">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="ceb71-385">イベントハンドラーを使用したり、既定のアクションシナリオを回避したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-385">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="ceb71-386">イベントの伝達の停止</span><span class="sxs-lookup"><span data-stu-id="ceb71-386">Stop event propagation</span></span>

<span data-ttu-id="ceb71-387">イベントの伝達を停止するには、 [@on{EVENT}: stopPropagation](xref:mvc/views/razor#oneventstoppropagation)ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-387">Use the [@on{EVENT}:stopPropagation](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="ceb71-388">次の例では、チェックボックスをオンにすると、2番目の子 `<div>` からのクリックイベントが親 `<div>`に反映されなくなります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-388">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```cshtml
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

::: moniker-end

## <a name="chained-bind"></a><span data-ttu-id="ceb71-389">チェーンバインド</span><span class="sxs-lookup"><span data-stu-id="ceb71-389">Chained bind</span></span>

<span data-ttu-id="ceb71-390">一般的なシナリオでは、データバインドパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-390">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="ceb71-391">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーンバインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-391">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="ceb71-392">チェーンバインドは、ページの要素で `@bind` 構文を使用して実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-392">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="ceb71-393">イベントハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-393">The event handler and value must be specified separately.</span></span> <span data-ttu-id="ceb71-394">ただし、親コンポーネントでは、`@bind` 構文をコンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-394">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="ceb71-395">次の `PasswordField` コンポーネント (*Passwordfield*):</span><span class="sxs-lookup"><span data-stu-id="ceb71-395">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="ceb71-396">`Password` プロパティに `<input>` 要素の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-396">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="ceb71-397">[Eventcallback](#eventcallback)を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-397">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

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

<span data-ttu-id="ceb71-398">`PasswordField` コンポーネントが別のコンポーネントで使用されています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-398">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="ceb71-399">前の例で、パスワードの確認またはトラップエラーを実行するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-399">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="ceb71-400">`Password` のバッキングフィールドを作成します (次のコード例では`password`)。</span><span class="sxs-lookup"><span data-stu-id="ceb71-400">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="ceb71-401">`Password` setter で、チェックまたはトラップのエラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-401">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="ceb71-402">次の例では、パスワードの値にスペースが使用されている場合に、ユーザーにすぐにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-402">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="capture-references-to-components"></a><span data-ttu-id="ceb71-403">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="ceb71-403">Capture references to components</span></span>

<span data-ttu-id="ceb71-404">コンポーネント参照を使用すると、`Show` や `Reset`などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-404">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="ceb71-405">コンポーネント参照をキャプチャするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-405">To capture a component reference:</span></span>

* <span data-ttu-id="ceb71-406">子コンポーネントに[@ref](xref:mvc/views/razor#ref)属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-406">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="ceb71-407">子コンポーネントと同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-407">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="ceb71-408">コンポーネントがレンダリングされると、[`loginDialog`] フィールドに `MyLoginDialog` 子コンポーネントインスタンスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-408">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="ceb71-409">その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-409">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ceb71-410">`loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-410">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="ceb71-411">この時点までは、参照するものはありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-411">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="ceb71-412">コンポーネント参照のレンダリングが完了した後にコンポーネント参照を操作するに[は、OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](#lifecycle-methods)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-412">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](#lifecycle-methods).</span></span>

<span data-ttu-id="ceb71-413">コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-413">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="ceb71-414">コンポーネント参照は、.NET コードでのみ使用される&mdash;JavaScript コードに渡されません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-414">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="ceb71-415">子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-415">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="ceb71-416">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-416">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="ceb71-417">通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-417">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="ceb71-418">状態を更新するためにコンポーネントメソッドを外部に呼び出す</span><span class="sxs-lookup"><span data-stu-id="ceb71-418">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="ceb71-419"> は、`SynchronizationContext` を使用して、1つの論理スレッドの実行を強制します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-419"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="ceb71-420">Blazor によって発生するコンポーネントのライフサイクルメソッドとイベントコールバックは、この `SynchronizationContext`で実行されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-420">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="ceb71-421">タイマーやその他の通知など、外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazorの `SynchronizationContext`にディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-421">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="ceb71-422">たとえば、更新された状態のすべてのリッスンコンポーネントに通知できる通知*サービス*を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-422">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="ceb71-423">コンポーネントを更新するための `NotifierService` の使用法:</span><span class="sxs-lookup"><span data-stu-id="ceb71-423">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="ceb71-424">前の例では、`NotifierService` は Blazorの `SynchronizationContext`の外部でコンポーネントの `OnNotify` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-424">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="ceb71-425">`InvokeAsync` は、正しいコンテキストに切り替え、レンダリングをキューに移動するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-425">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="ceb71-426">\@キーを使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="ceb71-426">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="ceb71-427">要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazorの比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-427">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="ceb71-428">通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-428">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="ceb71-429">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-429">Consider the following example:</span></span>

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

<span data-ttu-id="ceb71-430">`People` コレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-430">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="ceb71-431">コンポーネントのれ時に、`<DetailsEditor>` コンポーネントが異なる `Details` パラメーター値を受け取るように変更されることがあります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-431">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="ceb71-432">これにより、予想よりも複雑な再リリースが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-432">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="ceb71-433">場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-433">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="ceb71-434">マッピングプロセスは、`@key` ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-434">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="ceb71-435">`@key` によって、キーの値に基づいて要素またはコンポーネントの保持が比較アルゴリズムによって保証されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-435">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="ceb71-436">`People` コレクションが変更されると、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンスの間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-436">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="ceb71-437">`Person` が `People` の一覧から削除された場合は、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-437">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="ceb71-438">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-438">Other instances are left unchanged.</span></span>
* <span data-ttu-id="ceb71-439">リスト内のある位置に `Person` が挿入されると、その対応する位置に1つの新しい `<DetailsEditor>` インスタンスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-439">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="ceb71-440">その他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-440">Other instances are left unchanged.</span></span>
* <span data-ttu-id="ceb71-441">`Person` エントリが再順序付けされている場合は、対応する `<DetailsEditor>` インスタンスが UI に保持され、並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-441">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="ceb71-442">場合によっては、`@key` を使用すると、回避の複雑さが最小限に抑えられるため、フォーカス位置など、DOM のステートフルな部分に対する潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-442">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ceb71-443">キーは、各コンテナー要素またはコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-443">Keys are local to each container element or component.</span></span> <span data-ttu-id="ceb71-444">キーがドキュメント全体でグローバルに比較されることはありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-444">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="ceb71-445">\@キーを使用する場合</span><span class="sxs-lookup"><span data-stu-id="ceb71-445">When to use \@key</span></span>

<span data-ttu-id="ceb71-446">通常は、リストがレンダリングされるたびに (たとえば、`@foreach` ブロックで) `@key` を使用し、`@key`を定義するための適切な値が存在することを意味します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-446">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="ceb71-447">また、`@key` を使用すると、オブジェクトが変更されたときに Blazor によって要素またはコンポーネントのサブツリーが保持されないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-447">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="ceb71-448">`@currentPerson` が変更された場合、`@key` attribute ディレクティブは、`<div>` とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築する Blazor を強制的に実行します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-448">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="ceb71-449">これは、`@currentPerson` 変更時に UI の状態が保持されないことを保証する必要がある場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-449">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="ceb71-450">\@キーを使用しない場合</span><span class="sxs-lookup"><span data-stu-id="ceb71-450">When not to use \@key</span></span>

<span data-ttu-id="ceb71-451">`@key`を使用すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-451">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="ceb71-452">パフォーマンスコストは大きくありませんが、要素またはコンポーネントの保存規則を制御することによってアプリにメリットがある場合にのみ `@key` を指定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-452">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="ceb71-453">`@key` が使用されていない場合でも Blazor、子要素とコンポーネントインスタンスは可能な限り保持されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-453">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="ceb71-454">`@key` を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-454">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="ceb71-455">\@キーに使用する値</span><span class="sxs-lookup"><span data-stu-id="ceb71-455">What values to use for \@key</span></span>

<span data-ttu-id="ceb71-456">一般に、`@key`には、次のいずれかの値を指定するのが理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-456">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="ceb71-457">モデルオブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。</span><span class="sxs-lookup"><span data-stu-id="ceb71-457">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="ceb71-458">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-458">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="ceb71-459">一意の識別子 (たとえば、`int`型、`string`型、`Guid`型の主キー値)。</span><span class="sxs-lookup"><span data-stu-id="ceb71-459">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="ceb71-460">`@key` に使用される値が競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-460">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="ceb71-461">競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-461">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="ceb71-462">個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-462">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="ceb71-463">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="ceb71-463">Lifecycle methods</span></span>

<span data-ttu-id="ceb71-464">`OnInitializedAsync` し、`OnInitialized` 実行して、コンポーネントを初期化します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-464">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="ceb71-465">非同期操作を実行するには、操作で `OnInitializedAsync` と `await` キーワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-465">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="ceb71-466">`OnInitializedAsync` のライフサイクルイベント中に、コンポーネントの初期化時に非同期作業を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-466">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="ceb71-467">同期操作の場合は、`OnInitialized`を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-467">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="ceb71-468">コンポーネントが親からパラメーターを受け取り、値がプロパティに割り当てられると、`OnParametersSetAsync` と `OnParametersSet` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-468">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="ceb71-469">これらのメソッドは、コンポーネントの初期化の後、および親コンポーネントがレンダリングされるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-469">These methods are executed after component initialization and each time the parent component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="ceb71-470">パラメーターとプロパティ値を適用するときの非同期処理は、`OnParametersSetAsync` ライフサイクルイベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-470">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="ceb71-471">`OnAfterRenderAsync` と `OnAfterRender` は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-471">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="ceb71-472">この時点で、要素参照とコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-472">Element and component references are populated at this point.</span></span> <span data-ttu-id="ceb71-473">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-473">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="ceb71-474">*サーバーでのプリレンダリング時に `OnAfterRender` は呼び出されません。*</span><span class="sxs-lookup"><span data-stu-id="ceb71-474">`OnAfterRender` *isn't called when prerendering on the server.*</span></span>

<span data-ttu-id="ceb71-475">`OnAfterRenderAsync` と `OnAfterRender` の `firstRender` パラメーターは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-475">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="ceb71-476">コンポーネントインスタンスを初めて呼び出すときに `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-476">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="ceb71-477">初期化作業が1回だけ実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-477">Ensures that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="ceb71-478">`OnAfterRenderAsync` のライフサイクルイベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-478">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="ceb71-479">レンダー時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="ceb71-479">Handle incomplete async actions at render</span></span>

<span data-ttu-id="ceb71-480">ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-480">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="ceb71-481">ライフサイクルメソッドの実行中に、オブジェクトが `null` されているか、データが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-481">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="ceb71-482">オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-482">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="ceb71-483">オブジェクトの `null`中に、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-483">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="ceb71-484">Blazor テンプレートの `FetchData` コンポーネントでは、`OnInitializedAsync` がユーザー receive 予測データ (`forecasts`) に上書きされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-484">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="ceb71-485">`forecasts` が `null`と、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-485">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="ceb71-486">`OnInitializedAsync` によって返された `Task` が完了すると、コンポーネントは更新された状態とピアリングされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-486">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="ceb71-487">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-487">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="ceb71-488">パラメーターが設定される前にコードを実行する</span><span class="sxs-lookup"><span data-stu-id="ceb71-488">Execute code before parameters are set</span></span>

<span data-ttu-id="ceb71-489">`SetParameters` は、パラメーターを設定する前にコードを実行するようにオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-489">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="ceb71-490">`base.SetParameters` が呼び出されない場合、カスタムコードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-490">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="ceb71-491">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-491">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="ceb71-492">UI の更新を抑制する</span><span class="sxs-lookup"><span data-stu-id="ceb71-492">Suppress refreshing of the UI</span></span>

<span data-ttu-id="ceb71-493">`ShouldRender` は、UI の更新を抑制するためにオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-493">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="ceb71-494">実装によって `true`が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-494">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="ceb71-495">`ShouldRender` がオーバーライドされた場合でも、コンポーネントは常に最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-495">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="ceb71-496">IDisposable によるコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="ceb71-496">Component disposal with IDisposable</span></span>

<span data-ttu-id="ceb71-497">コンポーネントが <xref:System.IDisposable>を実装している場合は、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-497">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="ceb71-498">次のコンポーネントでは、`@implements IDisposable` と `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-498">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

> [!NOTE]
> <span data-ttu-id="ceb71-499">`Dispose` での `StateHasChanged` の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-499">Calling `StateHasChanged` in `Dispose` isn't supported.</span></span> <span data-ttu-id="ceb71-500">`StateHasChanged` は、破棄されているレンダラーの一部として呼び出されることがあります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-500">`StateHasChanged` might be invoked as part of the renderer being torn down.</span></span> <span data-ttu-id="ceb71-501">その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-501">Requesting UI updates at that point isn't supported.</span></span>

## <a name="routing"></a><span data-ttu-id="ceb71-502">ルーティング</span><span class="sxs-lookup"><span data-stu-id="ceb71-502">Routing</span></span>

<span data-ttu-id="ceb71-503">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-503">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="ceb71-504">`@page` ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-504">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="ceb71-505">実行時に、ルーターは `RouteAttribute` を持つコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-505">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="ceb71-506">コンポーネントには、複数のルートテンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-506">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="ceb71-507">次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute`に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-507">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="ceb71-508">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="ceb71-508">Route parameters</span></span>

<span data-ttu-id="ceb71-509">コンポーネントは、`@page` ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-509">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="ceb71-510">ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-510">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="ceb71-511">*ルートパラメーターコンポーネント*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-511">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="ceb71-512">省略可能なパラメーターはサポートされていないため、上記の例では2つの `@page` ディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-512">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="ceb71-513">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-513">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ceb71-514">2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-514">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a><span data-ttu-id="ceb71-515">部分クラスのサポート</span><span class="sxs-lookup"><span data-stu-id="ceb71-515">Partial class support</span></span>

<span data-ttu-id="ceb71-516">Razor コンポーネントは部分クラスとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-516">Razor components are generated as partial classes.</span></span> <span data-ttu-id="ceb71-517">Razor コンポーネントは、次のいずれかの方法を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-517">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="ceb71-518">C#コードは、1つのファイル内に HTML マークアップと Razor コードを含む[@code](xref:mvc/views/razor#code)ブロックで定義されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-518">C# code is defined in an [@code](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="ceb71-519"> テンプレートでは、この方法を使用して Razor コンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-519"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="ceb71-520">C#コードは、部分クラスとして定義されている分離コードファイルに配置されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-520">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="ceb71-521">次の例は、Blazor テンプレートから生成されたアプリで `@code` ブロックを持つ既定の `Counter` コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-521">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="ceb71-522">HTML マークアップ、Razor コード、 C#およびコードは、同じファイル内にあります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-522">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="ceb71-523">*Counter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-523">*Counter.razor*:</span></span>

```cshtml
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="ceb71-524">`Counter` コンポーネントは、部分クラスの分離コードファイルを使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-524">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="ceb71-525">*Counter。 razor*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-525">*Counter.razor*:</span></span>

```cshtml
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="ceb71-526">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-526">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

## <a name="specify-a-component-base-class"></a><span data-ttu-id="ceb71-527">コンポーネントの基本クラスを指定する</span><span class="sxs-lookup"><span data-stu-id="ceb71-527">Specify a component base class</span></span>

<span data-ttu-id="ceb71-528">`@inherits` ディレクティブは、コンポーネントの基底クラスを指定するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-528">The `@inherits` directive can be used to specify a base class for a component.</span></span>

<span data-ttu-id="ceb71-529">この[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)は、コンポーネントが基底クラス `BlazorRocksBase`を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-529">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="ceb71-530">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-530">*Pages/BlazorRocks.razor*:</span></span>

```cshtml
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="ceb71-531">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="ceb71-531">*BlazorRocksBase.cs*:</span></span>

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

<span data-ttu-id="ceb71-532">基底クラスは `ComponentBase`から派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-532">The base class should derive from `ComponentBase`.</span></span>

::: moniker-end

## <a name="import-components"></a><span data-ttu-id="ceb71-533">コンポーネントのインポート</span><span class="sxs-lookup"><span data-stu-id="ceb71-533">Import components</span></span>

<span data-ttu-id="ceb71-534">Razor で作成されるコンポーネントの名前空間は、(優先順位に従って) に基づきます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-534">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="ceb71-535">Razor ファイル (*razor*) マークアップ (`@namespace BlazorSample.MyNamespace`) の[@namespace](xref:mvc/views/razor#namespace)指定。</span><span class="sxs-lookup"><span data-stu-id="ceb71-535">[@namespace](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="ceb71-536">プロジェクトファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。</span><span class="sxs-lookup"><span data-stu-id="ceb71-536">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="ceb71-537">プロジェクトファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクトルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="ceb71-537">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="ceb71-538">たとえば、フレームワークは *{PROJECT ROOT}/* *BlazorSample (* ) を名前空間 `BlazorSample.Pages`に解決します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-538">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="ceb71-539">コンポーネントはC# 、名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="ceb71-539">Components follow C# name binding rules.</span></span> <span data-ttu-id="ceb71-540">この例の `Index` コンポーネントの場合、スコープ内のコンポーネントはすべてコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-540">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="ceb71-541">同じフォルダー内の*ページ*。</span><span class="sxs-lookup"><span data-stu-id="ceb71-541">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="ceb71-542">別の名前空間を明示的に指定していない、プロジェクトのルート内のコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ceb71-542">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="ceb71-543">別の名前空間で定義されているコンポーネントは、Razor の[@using](xref:mvc/views/razor#using)ディレクティブを使用してスコープ内に置かれます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-543">Components defined in a different namespace are brought into scope using Razor's [@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="ceb71-544">*BlazorSample/Shared/* フォルダーに別のコンポーネント (`NavMenu.razor`) が存在する場合は、次の `@using` ステートメントを使用して `Index.razor` でコンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-544">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="ceb71-545">コンポーネントは、完全修飾名を使用して参照することもできます。この場合、 [@using](xref:mvc/views/razor#using)ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-545">Components can also be referenced using their fully qualified names, which doesn't require the [@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="ceb71-546">`global::` の修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-546">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="ceb71-547">エイリアス付き `using` ステートメント (`@using Foo = Bar`など) を含むコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-547">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="ceb71-548">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-548">Partially qualified names aren't supported.</span></span> <span data-ttu-id="ceb71-549">たとえば、`<Shared.NavMenu></Shared.NavMenu>` での `@using BlazorSample` の追加と `NavMenu.razor` の参照はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-549">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="ceb71-550">条件付き HTML 要素の属性</span><span class="sxs-lookup"><span data-stu-id="ceb71-550">Conditional HTML element attributes</span></span>

<span data-ttu-id="ceb71-551">HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-551">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="ceb71-552">値が `false` または `null`の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-552">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="ceb71-553">値が `true`場合は、属性が最小化されて表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-553">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="ceb71-554">次の例では、`IsCompleted` によって `checked` が要素のマークアップに表示されるかどうかが決まります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-554">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="ceb71-555">`IsCompleted` が `true`場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-555">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="ceb71-556">`IsCompleted` が `false`場合、このチェックボックスは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-556">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="ceb71-557">詳細については、「 <xref:mvc/views/razor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-557">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="ceb71-558">[Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)などの一部の HTML 属性は、.net 型が `bool`の場合、正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-558">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="ceb71-559">そのような場合は、`bool`ではなく `string` の型を使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-559">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="ceb71-560">未加工の HTML</span><span class="sxs-lookup"><span data-stu-id="ceb71-560">Raw HTML</span></span>

<span data-ttu-id="ceb71-561">通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-561">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="ceb71-562">生の HTML をレンダリングするには、`MarkupString` 値で HTML コンテンツをラップします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-562">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="ceb71-563">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-563">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="ceb71-564">信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-564">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="ceb71-565">次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-565">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="ceb71-566">テンプレートコンポーネント</span><span class="sxs-lookup"><span data-stu-id="ceb71-566">Templated components</span></span>

<span data-ttu-id="ceb71-567">テンプレートコンポーネントは、1つまたは複数の UI テンプレートをパラメーターとして受け取り、コンポーネントのレンダリングロジックの一部として使用できるコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-567">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="ceb71-568">テンプレート化されたコンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-568">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="ceb71-569">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-569">A couple of examples include:</span></span>

* <span data-ttu-id="ceb71-570">テーブルのヘッダー、行、およびフッターのテンプレートをユーザーが指定できるようにするテーブルコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ceb71-570">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="ceb71-571">リスト内の項目を表示するためのテンプレートをユーザーが指定できるようにするリストコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ceb71-571">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="ceb71-572">テンプレート パラメーター</span><span class="sxs-lookup"><span data-stu-id="ceb71-572">Template parameters</span></span>

<span data-ttu-id="ceb71-573">`RenderFragment` または `RenderFragment<T>`型の1つ以上のコンポーネントパラメーターを指定して、テンプレート化されたコンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-573">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="ceb71-574">レンダーフラグメントは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-574">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="ceb71-575">`RenderFragment<T>` は、レンダリングフラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-575">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="ceb71-576">`TableTemplate` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ceb71-576">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="ceb71-577">テンプレート化されたコンポーネントを使用する場合、テンプレートパラメーターは、パラメーターの名前と一致する子要素を使用して指定できます (`TableHeader` と、次の例の `RowTemplate`)。</span><span class="sxs-lookup"><span data-stu-id="ceb71-577">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="ceb71-578">テンプレートコンテキストパラメーター</span><span class="sxs-lookup"><span data-stu-id="ceb71-578">Template context parameters</span></span>

<span data-ttu-id="ceb71-579">要素として渡される型 `RenderFragment<T>` のコンポーネント引数には、`context` という名前の暗黙的なパラメーターがあります (たとえば、上記のコードサンプルでは `@context.PetId`)。ただし、子要素の `Context` 属性を使用してパラメーター名を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-579">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="ceb71-580">次の例では、`RowTemplate` 要素の `Context` 属性で `pet` パラメーターを指定しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-580">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="ceb71-581">または、コンポーネント要素の `Context` 属性を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-581">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="ceb71-582">指定された `Context` 属性は、指定されたすべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-582">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="ceb71-583">これは、暗黙的な子コンテンツ (ラップする子要素を持たない) のコンテンツパラメーター名を指定する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-583">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="ceb71-584">次の例では、`Context` 属性が `TableTemplate` 要素に表示され、すべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-584">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="ceb71-585">汎用型のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="ceb71-585">Generic-typed components</span></span>

<span data-ttu-id="ceb71-586">多くの場合、テンプレート化されたコンポーネントは一般的に型指定されます</span><span class="sxs-lookup"><span data-stu-id="ceb71-586">Templated components are often generically typed.</span></span> <span data-ttu-id="ceb71-587">たとえば、汎用 `ListViewTemplate` コンポーネントを使用して `IEnumerable<T>` 値を表示できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-587">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="ceb71-588">ジェネリックコンポーネントを定義するには、 [@typeparam](xref:mvc/views/razor#typeparam)ディレクティブを使用して型パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-588">To define a generic component, use the [@typeparam](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="ceb71-589">ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-589">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="ceb71-590">それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-590">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="ceb71-591">次の例では、`TItem="Pet"` 型を指定しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-591">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="ceb71-592">カスケード値とパラメーター</span><span class="sxs-lookup"><span data-stu-id="ceb71-592">Cascading values and parameters</span></span>

<span data-ttu-id="ceb71-593">場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-593">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="ceb71-594">カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-594">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="ceb71-595">カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-595">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="ceb71-596">テーマの例</span><span class="sxs-lookup"><span data-stu-id="ceb71-596">Theme example</span></span>

<span data-ttu-id="ceb71-597">次のサンプルアプリの例では、`ThemeInfo` クラスが、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-597">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="ceb71-598">*UiThemeInfo/* :</span><span class="sxs-lookup"><span data-stu-id="ceb71-598">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="ceb71-599">先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-599">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="ceb71-600">`CascadingValue` コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-600">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="ceb71-601">たとえば、サンプルアプリでは、アプリのいずれかのレイアウトのテーマ情報 (`ThemeInfo`) を、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとして指定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-601">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="ceb71-602">`ButtonClass` には、レイアウトコンポーネントで `btn-success` の値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-602">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="ceb71-603">すべての子孫コンポーネントは、`ThemeInfo` カスケードオブジェクトを介してこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-603">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="ceb71-604">`CascadingValuesParametersLayout` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ceb71-604">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="ceb71-605">カスケード値を使用するために、コンポーネントは `[CascadingParameter]` 属性を使用してカスケード型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-605">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="ceb71-606">カスケード値は、型によってカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-606">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="ceb71-607">サンプルアプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値がカスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-607">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="ceb71-608">パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-608">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="ceb71-609">`CascadingValuesParametersTheme` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ceb71-609">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="ceb71-610">同じサブツリー内で同じ型の複数の値を連鎖させるには、各 `CascadingValue` コンポーネントとそれに対応する `CascadingParameter`に一意の `Name` 文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-610">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="ceb71-611">次の例では、2つの `CascadingValue` コンポーネントが名前で `MyCascadingType` の異なるインスタンスをカスケードしています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-611">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="ceb71-612">子孫コンポーネントでは、カスケードされたパラメーターは、先祖コンポーネントの対応するカスケード値から名前で値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-612">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="ceb71-613">TabSet の例</span><span class="sxs-lookup"><span data-stu-id="ceb71-613">TabSet example</span></span>

<span data-ttu-id="ceb71-614">カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-614">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="ceb71-615">たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-615">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="ceb71-616">このサンプルアプリには、タブに実装されている `ITab` インターフェイスがあります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-616">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="ceb71-617">`CascadingValuesParametersTabSet` コンポーネントは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-617">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="ceb71-618">子 `Tab` コンポーネントは、パラメーターとして `TabSet`に明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-618">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="ceb71-619">代わりに、子 `Tab` コンポーネントは、`TabSet`の子コンテンツの一部になります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-619">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="ceb71-620">ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントは、*それ自体をカスケード値として提供*し、その後、子孫 `Tab` コンポーネントによって取得されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-620">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="ceb71-621">`TabSet` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ceb71-621">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="ceb71-622">子孫の `Tab` コンポーネントは、含まれている `TabSet` をカスケード型パラメーターとしてキャプチャするので、`Tab` のコンポーネントは、どのタブがアクティブであるかを `TabSet` および座標に追加します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-622">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="ceb71-623">`Tab` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ceb71-623">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="ceb71-624">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="ceb71-624">Razor templates</span></span>

<span data-ttu-id="ceb71-625">レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-625">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="ceb71-626">Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-626">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="ceb71-627">次の例は、`RenderFragment` と `RenderFragment<T>` の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-627">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="ceb71-628">レンダーフラグメントは、[テンプレートコンポーネント](#templated-components)に引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-628">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="ceb71-629">前のコードの出力をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-629">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="ceb71-630">手動の RenderTreeBuilder ロジック</span><span class="sxs-lookup"><span data-stu-id="ceb71-630">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="ceb71-631">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` には、コンポーネントと要素を操作するためのメソッドがC#用意されています</span><span class="sxs-lookup"><span data-stu-id="ceb71-631">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="ceb71-632">コンポーネントの作成に `RenderTreeBuilder` を使用することは、高度なシナリオです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-632">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="ceb71-633">形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-633">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="ceb71-634">次の `PetDetails` コンポーネントを検討してください。これは手動で別のコンポーネントに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-634">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="ceb71-635">次の例では、`CreateComponent` メソッドのループによって、3つの `PetDetails` コンポーネントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-635">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="ceb71-636">`RenderTreeBuilder` メソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-636">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="ceb71-637">Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-637">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="ceb71-638">`RenderTreeBuilder` メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-638">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="ceb71-639">**計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。**</span><span class="sxs-lookup"><span data-stu-id="ceb71-639">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="ceb71-640">詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-640">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="ceb71-641">`BuiltContent` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ceb71-641">`BuiltContent` component:</span></span>

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

> <span data-ttu-id="ceb71-642">!要する`Microsoft.AspNetCore.Components.RenderTree` の型により、レンダリング操作の*結果*を処理できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-642">![WARNING] The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="ceb71-643">これらは、Blazor framework 実装の内部的な詳細です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-643">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="ceb71-644">これらの型は*不安定*であると見なされ、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-644">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="ceb71-645">シーケンス番号は、実行順序ではなくコード行番号に関連します</span><span class="sxs-lookup"><span data-stu-id="ceb71-645">Sequence numbers relate to code line numbers and not execution order</span></span>

Blazor<span data-ttu-id="ceb71-646"> `.razor` ファイルは常にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-646"> `.razor` files are always compiled.</span></span> <span data-ttu-id="ceb71-647">コンパイル手順を使用すると、実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、`.razor` にとって大きな利点があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-647">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="ceb71-648">これらの機能強化の主要な例には、*シーケンス番号*が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-648">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="ceb71-649">シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-649">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="ceb71-650">ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-650">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="ceb71-651">次の単純な `.razor` ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ceb71-651">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="ceb71-652">上記のコードは、次のようにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-652">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="ceb71-653">コードを初めて実行するときに、`someFlag` が `true`場合、ビルダーは次のメッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-653">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="ceb71-654">シーケンス</span><span class="sxs-lookup"><span data-stu-id="ceb71-654">Sequence</span></span> | <span data-ttu-id="ceb71-655">[種類]</span><span class="sxs-lookup"><span data-stu-id="ceb71-655">Type</span></span>      | <span data-ttu-id="ceb71-656">data</span><span class="sxs-lookup"><span data-stu-id="ceb71-656">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="ceb71-657">0</span><span class="sxs-lookup"><span data-stu-id="ceb71-657">0</span></span>        | <span data-ttu-id="ceb71-658">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="ceb71-658">Text node</span></span> | <span data-ttu-id="ceb71-659">First</span><span class="sxs-lookup"><span data-stu-id="ceb71-659">First</span></span>  |
| <span data-ttu-id="ceb71-660">1</span><span class="sxs-lookup"><span data-stu-id="ceb71-660">1</span></span>        | <span data-ttu-id="ceb71-661">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="ceb71-661">Text node</span></span> | <span data-ttu-id="ceb71-662">Second</span><span class="sxs-lookup"><span data-stu-id="ceb71-662">Second</span></span> |

<span data-ttu-id="ceb71-663">`someFlag` が `false`になり、マークアップが再び表示されるとします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-663">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="ceb71-664">この時点で、ビルダーは次のものを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-664">This time, the builder receives:</span></span>

| <span data-ttu-id="ceb71-665">シーケンス</span><span class="sxs-lookup"><span data-stu-id="ceb71-665">Sequence</span></span> | <span data-ttu-id="ceb71-666">[種類]</span><span class="sxs-lookup"><span data-stu-id="ceb71-666">Type</span></span>       | <span data-ttu-id="ceb71-667">data</span><span class="sxs-lookup"><span data-stu-id="ceb71-667">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="ceb71-668">1</span><span class="sxs-lookup"><span data-stu-id="ceb71-668">1</span></span>        | <span data-ttu-id="ceb71-669">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="ceb71-669">Text node</span></span>  | <span data-ttu-id="ceb71-670">Second</span><span class="sxs-lookup"><span data-stu-id="ceb71-670">Second</span></span> |

<span data-ttu-id="ceb71-671">ランタイムが diff を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な*編集スクリプト*が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-671">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="ceb71-672">最初のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-672">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="ceb71-673">プログラムによってシーケンス番号を生成した場合の問題</span><span class="sxs-lookup"><span data-stu-id="ceb71-673">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="ceb71-674">代わりに、次のレンダーツリービルダーのロジックを記述したとします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-674">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="ceb71-675">これで、最初の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-675">Now, the first output is:</span></span>

| <span data-ttu-id="ceb71-676">シーケンス</span><span class="sxs-lookup"><span data-stu-id="ceb71-676">Sequence</span></span> | <span data-ttu-id="ceb71-677">[種類]</span><span class="sxs-lookup"><span data-stu-id="ceb71-677">Type</span></span>      | <span data-ttu-id="ceb71-678">data</span><span class="sxs-lookup"><span data-stu-id="ceb71-678">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="ceb71-679">0</span><span class="sxs-lookup"><span data-stu-id="ceb71-679">0</span></span>        | <span data-ttu-id="ceb71-680">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="ceb71-680">Text node</span></span> | <span data-ttu-id="ceb71-681">First</span><span class="sxs-lookup"><span data-stu-id="ceb71-681">First</span></span>  |
| <span data-ttu-id="ceb71-682">1</span><span class="sxs-lookup"><span data-stu-id="ceb71-682">1</span></span>        | <span data-ttu-id="ceb71-683">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="ceb71-683">Text node</span></span> | <span data-ttu-id="ceb71-684">Second</span><span class="sxs-lookup"><span data-stu-id="ceb71-684">Second</span></span> |

<span data-ttu-id="ceb71-685">この結果は前のケースと同じであるため、負の問題は存在しません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-685">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="ceb71-686">2番目のレンダリングでは `someFlag` が `false`、出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-686">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="ceb71-687">シーケンス</span><span class="sxs-lookup"><span data-stu-id="ceb71-687">Sequence</span></span> | <span data-ttu-id="ceb71-688">[種類]</span><span class="sxs-lookup"><span data-stu-id="ceb71-688">Type</span></span>      | <span data-ttu-id="ceb71-689">data</span><span class="sxs-lookup"><span data-stu-id="ceb71-689">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="ceb71-690">0</span><span class="sxs-lookup"><span data-stu-id="ceb71-690">0</span></span>        | <span data-ttu-id="ceb71-691">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="ceb71-691">Text node</span></span> | <span data-ttu-id="ceb71-692">Second</span><span class="sxs-lookup"><span data-stu-id="ceb71-692">Second</span></span> |

<span data-ttu-id="ceb71-693">今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-693">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="ceb71-694">最初のテキストノードの値を `Second`に変更します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-694">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="ceb71-695">2番目のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-695">Remove the second text node.</span></span>

<span data-ttu-id="ceb71-696">シーケンス番号を生成すると、`if/else` 分岐とループが元のコードに存在する場所に関する有用な情報がすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-696">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="ceb71-697">これにより、diff は前の**2 倍**になります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-697">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="ceb71-698">これは簡単な例です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-698">This is a trivial example.</span></span> <span data-ttu-id="ceb71-699">複雑で深く入れ子になった構造、特にループを使用したより現実的なケースでは、パフォーマンスコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-699">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="ceb71-700">どのループブロックまたは分岐が挿入または削除されたかをすぐに識別するのではなく、diff アルゴリズムでは、レンダリングツリーに深く再帰する必要があります相互に関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-700">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="ceb71-701">ガイダンスと結論</span><span class="sxs-lookup"><span data-stu-id="ceb71-701">Guidance and conclusions</span></span>

* <span data-ttu-id="ceb71-702">シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-702">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="ceb71-703">コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-703">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="ceb71-704">手動で実装された `RenderTreeBuilder` ロジックの長いブロックは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-704">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="ceb71-705">`.razor` ファイルを優先し、コンパイラがシーケンス番号を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-705">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="ceb71-706">手動の `RenderTreeBuilder` ロジックを回避できない場合は、長いブロックのコードを `OpenRegion`/`CloseRegion` 呼び出しでラップされた小さな部分に分割します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-706">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="ceb71-707">各リージョンにはシーケンス番号の個別のスペースがあるため、各リージョン内のゼロ (または任意の任意の数) から再起動できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-707">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="ceb71-708">シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-708">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="ceb71-709">初期値とギャップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-709">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="ceb71-710">正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。</span><span class="sxs-lookup"><span data-stu-id="ceb71-710">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="ceb71-711"> はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-711"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="ceb71-712">シーケンス番号を使用すると比較ははるかに高速になり*ます。* また、Blazor には、開発者が razor ファイルを作成するときにシーケンス番号を自動的に処理するコンパイル手順の利点があります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-712">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="ceb71-713">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="ceb71-713">Localization</span></span>

Blazor<span data-ttu-id="ceb71-714"> サーバーアプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-714"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="ceb71-715">ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-715">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="ceb71-716">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-716">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="ceb71-717">Cookie</span><span class="sxs-lookup"><span data-stu-id="ceb71-717">Cookies</span></span>](#cookies)
* [<span data-ttu-id="ceb71-718">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="ceb71-718">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="ceb71-719">使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-719">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="ceb71-720">国際化のためのリンカーの構成 (Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ceb71-720">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="ceb71-721">既定では、BlazorBlazor WebAssembly のリンカー構成は、明示的に要求されたロケールを除き、国際化情報を除去します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-721">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ceb71-722">リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-722">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="ceb71-723">クッキー</span><span class="sxs-lookup"><span data-stu-id="ceb71-723">Cookies</span></span>

<span data-ttu-id="ceb71-724">ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-724">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="ceb71-725">Cookie は、アプリのホストページ (*Pages/host. cshtml. .cs*) の `OnGet` メソッドによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-725">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="ceb71-726">ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-726">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="ceb71-727">Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。</span><span class="sxs-lookup"><span data-stu-id="ceb71-727">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="ceb71-728">ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-728">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="ceb71-729">したがって、ローカライズカルチャ cookie を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-729">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="ceb71-730">カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-730">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="ceb71-731">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-731">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="ceb71-732">次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-732">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="ceb71-733">Blazor Server アプリで次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-733">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="ceb71-734">ローカライズはアプリで処理されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-734">Localization is handled in the app:</span></span>

1. <span data-ttu-id="ceb71-735">ブラウザーは、アプリに最初の HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-735">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="ceb71-736">カルチャは、ローカリゼーションミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-736">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="ceb71-737">*_Host*の `OnGet` メソッドは、応答の一部として cookie 内のカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-737">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="ceb71-738">ブラウザーは、WebSocket 接続を開き、対話型の Blazor サーバーセッションを作成します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-738">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="ceb71-739">ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-739">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="ceb71-740">Blazor サーバーセッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-740">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="ceb71-741">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="ceb71-741">Provide UI to choose the culture</span></span>

<span data-ttu-id="ceb71-742">ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-742">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="ceb71-743">このプロセスは、ユーザーがセキュリティで保護されたリソースにアクセスしようとしたときに、ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされる&mdash;、web アプリで発生する処理に似ています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-743">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="ceb71-744">アプリは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-744">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="ceb71-745">コントローラーは、ユーザーが選択したカルチャを cookie に設定し、ユーザーを元の URI にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ceb71-745">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="ceb71-746">Cookie でユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するために、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-746">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="ceb71-747">`LocalRedirect` アクションの結果を使用して、開いているリダイレクト攻撃を防止します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-747">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="ceb71-748">詳細については、「 <xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-748">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="ceb71-749">次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-749">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="ceb71-750">Blazor アプリで .NET ローカライズシナリオを使用する</span><span class="sxs-lookup"><span data-stu-id="ceb71-750">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="ceb71-751">Blazor アプリ内では、次の .NET ローカリゼーションとグローバリゼーションのシナリオを利用できます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-751">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="ceb71-752">.NET のリソースシステム</span><span class="sxs-lookup"><span data-stu-id="ceb71-752">.NET's resources system</span></span>
* <span data-ttu-id="ceb71-753">カルチャ固有の数値と日付の書式設定</span><span class="sxs-lookup"><span data-stu-id="ceb71-753">Culture-specific number and date formatting</span></span>

Blazor<span data-ttu-id="ceb71-754">の `@bind` 機能は、ユーザーの現在のカルチャに基づいてグローバリゼーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="ceb71-754">'s `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="ceb71-755">詳細については、「[データバインディング](#data-binding)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-755">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="ceb71-756">現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-756">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="ceb71-757">`IStringLocalizer<>` は Blazor アプリで*サポートされて*います。</span><span class="sxs-lookup"><span data-stu-id="ceb71-757">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="ceb71-758">`IHtmlLocalizer<>`、`IViewLocalizer<>`、データ注釈のローカライズは MVC シナリオ ASP.NET Core、Blazor アプリではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="ceb71-758">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="ceb71-759">詳細については、「 <xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ceb71-759">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="ceb71-760">スケーラブルベクターグラフィックス (SVG) イメージ</span><span class="sxs-lookup"><span data-stu-id="ceb71-760">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="ceb71-761">Blazor は HTML をレンダリングするため、スケーラブルベクターグラフィックス (svg) イメージ (*svg*) を含むブラウザーでサポートされているイメージは、`<img>` タグを介してサポートされます。</span><span class="sxs-lookup"><span data-stu-id="ceb71-761">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="ceb71-762">同様に、SVG イメージは、スタイルシートファイル ( *.css*) の css 規則でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-762">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="ceb71-763">ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-763">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="ceb71-764">コンポーネントファイル (*razor*) に `<svg>` タグを直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-764">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="ceb71-765">たとえば、`<use>` タグは現在尊重されていないため、`@bind` をいくつかの SVG タグと共に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="ceb71-765">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="ceb71-766">今後のリリースでは、これらの制限に対処する予定です。</span><span class="sxs-lookup"><span data-stu-id="ceb71-766">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ceb71-767">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ceb71-767">Additional resources</span></span>

* <span data-ttu-id="ceb71-768"><xref:security/blazor/server> &ndash; には、リソース枯渇に対処する必要がある Blazor サーバーアプリを構築するためのガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ceb71-768"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
